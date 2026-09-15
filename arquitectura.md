# Señor de Burgos — Arquitectura del sistema

**Documento técnico interno.** Preparado para que el personal técnico pueda explicar el sistema
y defender las decisiones de diseño.

- **Repositorio:** `senior-burgos-original`
- **Rama analizada:** `feat/validacion-arquitectura`
- **Commit:** `6524db9`
- **Fecha del análisis:** 15 de septiembre de 2026

**Tamaño:** ~4.300 líneas de Python (sin migraciones) y ~16.400 líneas de Vue/JS.
44 endpoints REST repartidos en 6 apps de Django.

> Este documento no está pensado para vivir dentro del repositorio: incluye una evaluación
> crítica con deuda técnica identificada. Es material de trabajo, no documentación pública.

---

## 1. Panorama general

Dos aplicaciones independientes que se comunican solo por HTTP/JSON:

```
┌─────────────────────────────┐         ┌──────────────────────────────┐
│  FRONTEND  (Vue 3 + Vite)   │         │  BACKEND  (Django + DRF)     │
│  SPA servida como estáticos │◄───────►│  API REST                    │
│                             │  JSON   │                              │
│  Puerto 5173 (dev)          │  + JWT  │  Puerto 8000 (dev)           │
└─────────────────────────────┘         └──────────────┬───────────────┘
                                                       │
                                                ┌──────▼───────┐
                                                │  PostgreSQL  │
                                                └──────────────┘
```

No hay renderizado en servidor ni plantillas de Django: el backend es exclusivamente API.
Esto permite desplegar y escalar cada lado por separado, y que el frontend pueda ser
reemplazado (por una app móvil, por ejemplo) sin tocar la lógica de negocio.

### Stack

| Capa | Tecnología | Versión |
|---|---|---|
| Backend | Django | 5.2.2 |
| API | Django REST Framework | 3.16.0 |
| Autenticación | djangorestframework-simplejwt | 5.5.0 |
| Imágenes | Pillow | 11.2.1 |
| Base de datos | PostgreSQL | — |
| Frontend | Vue 3 (Composition API, `<script setup>`) | — |
| Build | Vite | 5.4.21 |
| Estado | Pinia | — |
| Ruteo | Vue Router | — |
| Estilos | Tailwind CSS + CSS scoped | — |
| Iconos | lucide-vue-next | — |

---

## 2. Arquitectura del backend — 4 capas

La decisión estructural más importante del backend: **ninguna vista habla directamente
con el ORM**. Cada petición atraviesa cuatro capas con responsabilidades separadas.

```
  HTTP
   │
   ▼
┌──────────────┐   Traduce HTTP ↔ dominio. No decide reglas de negocio.
│    VIEW      │   Elige permisos, arma el serializer, devuelve códigos HTTP.
└──────┬───────┘
       │
       ▼
┌──────────────┐   Valida formato y sanea la entrada. No toca la base de datos.
│  SERIALIZER  │
└──────┬───────┘
       │
       ▼
┌──────────────┐   Las reglas del negocio viven acá. No sabe qué es HTTP.
│   SERVICE    │   Devuelve `Result`; lanza `DomainValidationError`.
└──────┬───────┘
       │
       ▼
┌──────────────┐   Único lugar que consulta el ORM. No conoce reglas.
│  REPOSITORY  │
└──────┬───────┘
       │
       ▼
┌──────────────┐   Estructura de datos y restricciones de integridad.
│    MODEL     │
└──────────────┘
```

### Por qué importa

Un ejemplo concreto del sistema. La regla *«no puede haber dos ediciones abiertas a la vez»*
vive en un solo lugar:

```python
# apps/ediciones/services/edicion_service.py
def crear_edicion(self, datos: dict) -> Result:
    if EdicionRepository.existe_activa():
        raise DomainValidationError('Debe cerrar la edición actual antes de abrir una nueva.')

    datos['estado'] = EstadoChoices.ACTIVO
    edicion = EdicionRepository.crear(**datos)
    return Result.ok(data=edicion, message='Edición creada correctamente.', status_code=201)
```

La vista no sabe nada de esa regla; solo traduce el fallo a HTTP:

```python
# apps/ediciones/views/edicion_views.py
try:
    result = service.crear_edicion(serializer.validated_data)
except DomainValidationError as e:
    return Response({'detail': str(e)}, status=status.HTTP_400_BAD_REQUEST)
```

**Consecuencia práctica:** si mañana esa regla se necesita desde un comando de consola,
una tarea programada o un webhook, se invoca el mismo servicio. No se duplica la regla
ni se reescribe la validación.

### Las apps

```
backend/apps/
├── usuarios/     Autenticación, roles, login con Google
├── ediciones/    El "año" del concurso; casi todo depende de la edición activa
├── alfombras/    Inscripción, moderación, votos ("me gusta"), ranking, ganadores
├── dinamica/     Trivia tipo Kahoot: categorías, preguntas, intentos, ganadores
├── anuncios/     Comunicados de la organización
└── galeria/      Publicaciones de fotos de la comunidad
```

`ediciones` es el eje temporal: las alfombras, los ganadores y la trivia se cuelgan de una
edición. Cerrar una edición es lo que convierte los datos en histórico consultable.

### Utilidades compartidas (`backend/utils/`)

Acá vive lo que, de no existir, se repetiría en cada app. Es el corazón del DRY del backend.

| Archivo | Responsabilidad |
|---|---|
| `result_utils.py` | Clase `Result` — objeto de retorno uniforme de los servicios (`ok`/`fail`/`warn` con `data`, `message`, `status_code`) |
| `exceptions.py` | `DomainValidationError` — la excepción que separa «regla de negocio violada» de «error técnico» |
| `serializer_mixin.py` | Campos y helpers reutilizables de serialización (detalle abajo) |
| `query_builder.py` | `QueryBuilderService.apply()` — búsqueda y ordenamiento seguros sobre cualquier QuerySet |
| `paginate.py` | `StandardPagination` — formato `{ data, meta }` único para todo el sistema |
| `throttles.py` | Límites de tasa de peticiones |

#### `serializer_mixin.py` en detalle

Es el archivo más reutilizado del backend:

- **`CleanCharField`** — campo de texto que elimina todo HTML antes de guardar.
  Defensa contra XSS aplicada por tipo de campo, no por recordatorio del programador.
- **`RichTextField`** — para el editor enriquecido: lista blanca de etiquetas seguras
  vía `bleach`, bloquea `<script>`, `<iframe>` y atributos `on*`.
- **`ImagenField`** — `ImageField` con tope de peso. Hereda de DRF, así que Pillow abre
  el archivo antes de aceptarlo: **un video renombrado a `.jpg` se rechaza solo**.
  Pillow no mira el tamaño, y por eso el límite de MB se valida acá.
- **`absolute_file_url()`** — centraliza `request.build_absolute_uri(campo.url)`.
  Sin esto, las imágenes llegan con rutas relativas que el navegador resuelve contra
  el origen equivocado.
- **`SanitizeTextMixin`**, **`DynamicFieldsMixin`** — saneado masivo y selección de
  campos por query param (`?fields=id,nombre`).

### Autenticación y autorización

**Tokens JWT** (`access` + `refresh`) con renovación transparente. El login es por email;
`username` es solo un apodo público.

**Dos roles**, en `RolChoices`: `admin` y `participante`.

La autorización se expresa como clases de permiso de DRF, en
`apps/usuarios/permissions/usuario_permissions.py`:

```python
class EsAdmin(BasePermission):        # solo administradores
class EsParticipante(BasePermission): # solo participantes (ni admin, ni anónimo)
class EsPropietario(BasePermission):  # solo el dueño del objeto
```

Y se aplican **por acción**, no por vista completa:

```python
# apps/alfombras/views/alfombra_views.py
def get_permissions(self):
    if self.action in ('galeria', 'ranking', 'retrieve'):
        return [AllowAny()]                    # público
    if self.action in ('pendientes', 'aprobar', 'rechazar'):
        return [EsAdmin()]                     # moderación
    if self.action in ('create', 'calificar', 'quitar_me_gusta'):
        return [EsParticipante()]              # participar
    return [IsAuthenticated()]
```

**Punto a destacar en la presentación:** la galería es pública y la inscripción exige
sesión, en el *mismo* ViewSet. El control es granular por operación.

También hay **login con Google** (`apps/usuarios/services/google_auth_service.py`):
el frontend obtiene un `credential` de Google y el backend lo verifica y emite sus
propios JWT. El usuario queda ligado por `google_id`.

---

## 3. Arquitectura del frontend — modular por dominio

El frontend **no** está organizado por tipo de archivo (todas las vistas juntas, todos
los servicios juntos). Está organizado por **módulo de negocio**, y cada módulo es
autocontenido:

```
src/modules/alfombras/
├── routes.js                    Sus propias rutas
├── service.js                   Sus llamadas HTTP
├── composables/                 Su estado y lógica
│   ├── useGaleriaAlfombras.js
│   └── useMisAlfombras.js
└── views/
    ├── GaleriaAlfombras.vue     Vista de página
    └── partes/                  Piezas internas de esa vista
        ├── TarjetaAlfombra.vue
        ├── BotonVoto.vue
        ├── CarruselFotos.vue
        ├── PodioRanking.vue
        ├── FilaRanking.vue
        ├── FormularioInscripcion.vue
        └── PanelMisAlfombras.vue
```

**Ventaja para el equipo:** para trabajar en alfombras se abre una carpeta. Para dar de
baja el módulo, se borra una carpeta y una línea del router. Los módulos no se enteran
unos de otros.

### Separación público / administración

```
src/modules/
├── alfombras/  anuncios/  dinamica/  ganadores/   ← cara pública
├── auth/                                          ← login y registro
├── landing/                                       ← portada e historia
└── admin/
    ├── alfombras/  anuncios/  dinamica/           ← panel interno
    └── ediciones/  usuarios/
```

Son módulos distintos porque resuelven problemas distintos: el público prioriza
presentación e interacción; el admin prioriza densidad de datos y operaciones masivas.
Comparten el cliente HTTP y los componentes de formulario, no las vistas.

### El patrón de 3 archivos

Dentro de cada módulo se repite la misma división de responsabilidades:

```
service.js      →  QUÉ se le pide al servidor      (solo HTTP, cero estado)
composable.js   →  CÓMO se maneja ese estado       (refs, loading, errores, reglas de UI)
views/*.vue     →  CÓMO se ve                      (plantilla y estilos)
```

Ejemplo real del servicio de alfombras:

```javascript
// modules/alfombras/service.js — una línea por endpoint, nada más
const alfombrasService = {
    galeria:       (params)    => api.get('/alfombras/galeria/', { params }),
    ranking:       (params)    => api.get('/alfombras/ranking/', { params }),
    misAlfombras:  (params)    => api.get('/alfombras/mis-alfombras/', { params }),
    inscribir:     (formData)  => api.post('/alfombras/', formData),
    calificar:     (id, data)  => api.post(`/alfombras/${id}/calificar/`, data),
    quitarMeGusta: (id)        => api.delete(`/alfombras/${id}/calificar/`),
    // admin
    pendientes:    (params)    => api.get('/alfombras/pendientes/', { params }),
    aprobar:       (id)        => api.patch(`/alfombras/${id}/aprobar/`),
    rechazar:      (id)        => api.patch(`/alfombras/${id}/rechazar/`),
}
```

Y el composable que lo consume, con toda la lógica de estado:

```javascript
// modules/alfombras/composables/useGaleriaAlfombras.js (resumido)
export function useGaleriaAlfombras() {
    const alfombras = ref([])
    const votadas   = ref(new Set())
    const votando   = ref(new Set())

    // El "me gusta" es un interruptor con respuesta optimista:
    // el corazón cambia al instante y se revierte si el servidor falla.
    const onMeGusta = async (alfombra) => {
        if (votando.value.has(alfombra.id)) return
        const quitando = votadas.value.has(alfombra.id)
        // … actualiza la UI, llama al servicio, revierte si hay error
    }

    return { alfombras, votadas, votando, onMeGusta, /* … */ }
}
```

**Por qué el composable y no el componente:** la vista queda declarativa y la lógica
queda testeable sin montar nada. `GaleriaAlfombras.vue` no sabe cómo se vota; solo
muestra lo que el composable expone.

### Infraestructura compartida

| Ubicación | Qué resuelve |
|---|---|
| `services/api.js` | Cliente Axios único: inyecta el JWT, arregla el `Content-Type` de los `FormData` y renueva el token al recibir un 401 |
| `stores/auth.js` | Sesión: token, refresh, usuario, rol, `isAuthenticated`, `isAdmin` |
| `stores/ui.js` | Estado de interfaz global (sidebar, loading) |
| `composables/useToasts.js` | Notificaciones (`success`, `error`, `info`, `warning`) |
| `composables/useConfirm.js` | Diálogos de confirmación como promesa: `if (await confirm({...}))` |
| `composables/useScrollReveal.js` | Directiva `v-reveal` — anima elementos al entrar en pantalla |
| `composables/useApi.js` | Envoltorio genérico `{ data, loading, error, execute }` |
| `helpers/apiError.js` | `apiError()` y `fieldErrors()` — normalizan los errores de DRF |
| `helpers/dateUtils.js` | Formato de fechas |
| `layouts/` | `AppLayout` (público), `AdmLayout` (panel), `AuthLayout` (login) |

#### Los dos interceptores de `api.js` — vale explicarlos

**1. Renovación de token en cola.** Si llegan 5 peticiones y todas reciben 401, no se
disparan 5 renovaciones: la primera renueva y las otras 4 esperan en una cola, luego
se reintentan con el token nuevo. Si la renovación falla, se cierra la sesión.

**2. El `Content-Type` de los `FormData`.** Cuando se sube una imagen, Axios *no* debe
fijar el `Content-Type`: el navegador tiene que ponerlo con el `boundary` del multipart.
Si queda en `application/json`, Axios convierte el `FormData` a texto y el backend
responde *«la información enviada no era un archivo»*. El interceptor lo elimina:

```javascript
if (config.data instanceof FormData) {
    config.headers.delete('Content-Type')
}
```

Se usa `.delete()` y no `delete config.headers['Content-Type']` porque `config.headers`
es una instancia de `AxiosHeaders`, que normaliza mayúsculas: un borrado por nombre
exacto fallaría si el header se fijara como `content-type`.

### Protección de rutas

`middleware/auth.js` expone dos guardas que el router aplica por rama:

```javascript
// router/index.js
{ path: '/admin', beforeEnter: adminGuard, children: [ /* módulos admin */ ] }
```

Un solo guard protege todo el panel. Las vistas públicas que necesitan sesión para
*una acción concreta* (inscribir una alfombra) no usan guard de ruta: la vista es
pública y la acción exige sesión en el momento de ejecutarla.

---

## 4. Componentes reutilizables

Este es el punto donde el proyecto muestra más madurez, y conviene presentarlo con
ejemplos concretos.

### `components/Publico/` — el sistema de diseño del sitio público

Estos 6 componentes nacieron de extraer código que ya estaba duplicado. No son
abstracciones especulativas.

| Componente | Props principales | Nació de |
|---|---|---|
| `HeroSeccion.vue` | `eyebrow`, `linea1`, `linea2`, `lead`, `ctaTexto`, `ctaTo`, `ctaIcono` | El hero de la galería, repetido en cada módulo |
| `EstadoVacio.vue` | `icono`, `texto`, `ctaTexto`, `ctaTo` | El "no hay nada acá" de cada listado |
| `GridSkeleton.vue` | `cantidad` | Las tarjetas fantasma de carga |
| `LightboxImagen.vue` | `imagenes`, `abierto`, `indice`, `alt`, `info` | Estaba duplicado en Alfombras y Anuncios |
| `ModalPublico.vue` | `abierto`, `titulo`, `subtitulo`, `ancho` | El equivalente oscuro de `DialogModal` (que es del admin) |
| `AtajosSeccion.vue` | `eyebrow`, `titulo`, `atajos[]` | Navegación "a dónde ir después" |

#### Caso de estudio: `LightboxImagen.vue`

El mejor ejemplo de reutilización real del proyecto. **El mismo componente sirve a tres
consumidores con necesidades distintas:**

1. **Alfombras (público)** — carrusel de 3 fotos, con flechas, puntos, contador y pie
   con título, lugar, autor y descripción.
2. **Anuncios (público)** — una sola imagen: los controles de navegación desaparecen
   solos y el pie muestra únicamente el título.
3. **Moderación (admin)** — el administrador revisa las 3 fotos en grande antes de
   aprobar. Mismo visor, contexto completamente distinto.

Cómo lo logra sin ramificar por caso:

```javascript
// Acepta ['url', …] o [{ imagen }, …]: no se ata a la forma del endpoint
const urls = computed(() =>
    props.imagenes.map((f) => (typeof f === 'string' ? f : f?.imagen)).filter(Boolean)
)

// Cada campo del pie es opcional: ausente = no se muestra
const titulo = computed(() => props.info?.titulo ?? props.alt)
const lugar  = computed(() => props.info?.lugar || '')

// Los controles aparecen solo si hacen falta
const hayVarias = computed(() => urls.value.length > 1)
```

Además **centraliza el comportamiento molesto**: tecla Escape, clic afuera, flechas del
teclado y bloqueo del scroll del `body`. Ningún consumidor repite ese manejo.

El índice y el estado abierto/cerrado los controla quien lo usa (`v-model:abierto`,
`v-model:indice`), lo que permite que el carrusel de fondo se mantenga sincronizado
con la foto que se está viendo.

#### Caso de estudio: `SelectorImagenes.vue`

Selector de varias imágenes con previsualización. Rechaza en el navegador lo que no
corresponde, mostrando el motivo:

```javascript
const motivoRechazo = (file) => {
    if (!file.type.startsWith('image/')) return 'no es una imagen'
    if (file.size > props.maxMb * 1024 * 1024) return `pesa más de ${props.maxMb} MB`
    return null
}
```

**Punto importante para defender en la presentación:** esta validación es *comodidad*,
no seguridad. El backend vuelve a validar lo mismo con Pillow, porque el navegador se
puede saltear. Las dos capas existen a propósito y cumplen funciones distintas:
el frontend evita un viaje inútil al servidor; el backend es la autoridad.

Convive con `ImageUpload.vue` (una sola imagen, panel admin) en lugar de reemplazarlo:
son dos casos de uso distintos, y forzar uno solo habría hecho el componente peor
para ambos.

#### Extensión sin romper: el CTA de `HeroSeccion`

Cuando "Mis alfombras" pasó de página a modal, el CTA del hero necesitaba **ejecutar una
acción** en vez de navegar. En lugar de crear otro componente o agregar un flag, se
extendió el contrato de forma retrocompatible:

```javascript
// Con `ctaTo` navega; sin él pero con `ctaTexto`, emite `cta`
<router-link v-if="ctaTexto && ctaTo" :to="ctaTo" class="hero__cta"> … </router-link>
<button v-else-if="ctaTexto" type="button" class="hero__cta" @click="$emit('cta')"> … </button>
```

Todos los usos existentes siguieron funcionando sin tocarlos. Lo mismo se hizo en
`EstadoVacio.vue`.

### Componentes internos de módulo (`views/partes/`)

No todo lo reutilizable es global. `TarjetaAlfombra`, `BotonVoto`, `PodioRanking` se usan
**dentro** de su módulo. Mantenerlos ahí es deliberado: subirlos a `components/` los
volvería API pública del proyecto sin que nadie más los necesite.

`PodioGanador.vue` (módulo ganadores) muestra la técnica para compartir estructura sin
acoplar dominio — **scoped slots**:

```vue
<PodioGanador titulo="Concurso de Alfombras" :icono="PaintBucket" :ganadores="podioAlfombras">
    <template #visual="{ ganador }">  <img :src="ganador.alfombra.primera_foto" />  </template>
    <template #nombre="{ ganador }">  {{ ganador.alfombra.titulo }}                 </template>
    <template #meta="{ ganador }">    <span>{{ ganador.alfombra.lugar }}</span>      </template>
</PodioGanador>
```

El componente aporta el podio (posiciones 2-1-3, corona, alturas, animaciones); el padre
aporta el contenido. La misma pieza sirve para alfombras (foto + lugar + autor) y para
la trivia (avatar + puntaje + tiempo) sin un solo `if` de dominio adentro.

### Inventario de componentes compartidos

```
components/
├── Publico/    6   Sistema de diseño del sitio público
├── Inputs/    15   Campos de formulario
├── Buttons/    8   Botones
├── Admin/     10   Tabla, paginación, toolbar, cabecera del panel
└── (raíz)      8   Navbar, Modal, DialogModal, ConfirmDialog, Toast, Loading…
```

---

## 5. Evaluación SOLID

### S — Responsabilidad única

**Se cumple bien en el backend.** Las 4 capas son una aplicación directa del principio:
cada clase tiene una razón para cambiar. Un cambio de reglas de negocio toca servicios;
un cambio de consultas toca repositorios; un cambio de contrato HTTP toca vistas.

**Se cumple bien en los módulos del frontend** con la división
`service` / `composable` / `vista`.

**Incumplimientos concretos:**

| Archivo | Líneas | Problema |
|---|---|---|
| `modules/landing/Historia.vue` | 682 | Plantilla, SVG inline, `IntersectionObserver` y ~400 líneas de CSS en un archivo |
| `components/Navbar.vue` | 551 | Menú escritorio, drawer móvil, submenús, menú de perfil y lógica de scroll juntos |
| `modules/landing/Principal.vue` | 360 | Misma mezcla que Historia |

Ninguno rompe nada hoy, pero son los archivos donde dos personas van a chocar al
trabajar en paralelo. El Navbar es el más urgente: lo toca cualquiera que agregue
una sección.

### O — Abierto/cerrado

**El mejor ejemplo del proyecto es `QueryBuilderService`:**

```python
queryset = QueryBuilderService.apply(
    queryset, params,
    allowed_fields=['nombre'],
    default_ordering=['-anio'],
)
```

Agregar búsqueda a una app nueva no requiere modificar el buscador: se le pasan los
campos permitidos. Además la lista blanca evita que un query param arbitrario ordene
por cualquier columna.

Otros casos: los campos de `serializer_mixin` se extienden por herencia
(`ImagenField(max_mb=8)`), y el router compone rutas por módulo sin modificar el
archivo central más allá de una línea.

**Donde no se cumple:** el router tiene un `catch-all` que manda todo lo desconocido a
`/login`. Cada ruta nueva mal escrita cae en login en vez de en un 404, lo que confunde.

### L — Sustitución de Liskov

Poco aplicable: hay poca herencia y la que hay es correcta.
`ImagenField(serializers.ImageField)` respeta el contrato del padre — sigue aceptando
lo que el padre acepta, solo restringe por tamaño. Las clases de permiso implementan
`BasePermission` sin cambiar la semántica.

### I — Segregación de interfaces

**Se cumple en el frontend.** Los componentes tienen props pequeñas y con
`default`, así que nadie está obligado a pasar lo que no usa. `LightboxImagen` funciona
con solo `imagenes` y `abierto`; `info` y `alt` son opcionales.

**Incumplimiento en el backend:** los serializers están segregados por caso de uso
(`AlfombraListSerializer`, `AlfombraDetailSerializer`, `RankingSerializer`,
`InscribirAlfombraSerializer`), lo cual es correcto — pero
`InscribirAlfombraSerializer` fuerza `fotos` obligatorio, y si mañana hiciera falta
una inscripción administrativa sin fotos habría que separarlo.

### D — Inversión de dependencias

**Parcialmente.** El flujo de dependencias es correcto —
vistas → servicios → repositorios — y las capas altas no dependen del ORM directamente.

**Pero la inyección es por importación estática, no por construcción:**

```python
class EdicionService:
    def __init__(self):
        self.repo = EdicionRepository()     # se asigna…

    def listar_todas(self, params) -> Result:
        queryset = EdicionRepository.get_all()   # …y no se usa: se llama la clase
```

`self.repo` queda declarado y nunca se utiliza. Los servicios llaman a los repositorios
como clases con métodos estáticos, lo que significa que **no se puede sustituir un
repositorio por un doble de prueba** sin parchear el módulo.

**Consecuencia real:** no hay tests automatizados en el proyecto, y esta decisión es una
de las razones por las que costaría escribirlos. Si se quisiera testear
`EdicionService.crear_edicion` sin base de datos, hoy no se puede de forma limpia.

---

## 6. Deuda técnica detectada

Ordenada por lo que conviene atender primero.

### Alta — código muerto que confunde

**16 componentes huérfanos** (sin un solo uso en el proyecto):

```
components/PrimaryButton.vue            ← duplicado exacto de…
components/Buttons/PrimaryButton.vue    ← …este otro, ambos sin usar
components/Buttons/ButtonSecondary.vue
components/Buttons/ButtonGradient.vue
components/Buttons/ButtonS1.vue
components/Buttons/ButtonTertiary.vue
components/Admin/ButtonMenu.vue
components/Admin/StepIndicator.vue
components/Admin/SucursalFilter.vue     ← "Sucursal" no existe en este dominio
components/Inputs/InputDNI.vue
components/Inputs/InputDuration.vue
components/Inputs/InputImage.vue
components/Inputs/RichTextEditor.vue
components/Inputs/Checkbox.vue
components/Inputs/LabelRequired.vue
components/Inputs/InputRadioCard.vue
```

`SucursalFilter.vue` es rastro de otra plantilla: no hay sucursales en el sistema.
Ocho de los quince componentes de `Inputs/` no se usan, lo que hace difícil saber cuál
es el correcto al agregar un formulario.

**`modules/landing/HistoriaBurgos.vue` — 414 líneas huérfanas.** El router usa
`Historia.vue`. Quedaron dos versiones de la misma página; conviene confirmar cuál es
la buena y borrar la otra antes de que alguien edite la equivocada.

### Alta — API sin consumidor

El backend expone `api/galeria/` con 5 endpoints y una app completa
(`apps/galeria/`: modelo, repositorio, servicio, serializer, vista). **El módulo
frontend de galería fue eliminado** en el commit `fa9703f`, pero el menú todavía
tiene un enlace a "Galería".

Decisión pendiente: rehacer el frontend o dar de baja la app. Hoy es superficie de API
publicada sin nadie que la use, y un enlace de menú que no lleva a ninguna parte.

### Media — inconsistencia de capas

`modules/admin/alfombras/` es el único módulo **sin `service.js` propio**: importa
`@/modules/alfombras/service.js`, que mezcla endpoints públicos y de administración
en un mismo objeto. Todos los demás módulos admin tienen su servicio.

No es un error funcional, pero rompe el patrón y hace que el servicio público cargue
métodos que solo usa el admin.

### Media — `self.repo` sin usar

Descrito en la sección D. Además de ser código muerto, sugiere una intención
(inyección de dependencias) que el código no cumple. Conviene o completarla o quitarla,
no dejarla a medias.

### Baja — detalles

- `apps/usuarios/views/googe_auth_views.py` — falta la `l` en "google".
- `components/Modal.vue` y `components/DialogModal.vue` conviven con
  `ModalPublico.vue`; la regla es clara (los dos primeros son del admin, el tercero
  del público) pero no está escrita en ningún lado.

### Transversal — sin tests automatizados

No hay suite de pruebas en ninguno de los dos lados. Toda la verificación es manual.
Con la arquitectura en capas que ya existe, los servicios del backend serían el lugar
más rentable para empezar: son lógica pura y ahí viven las reglas del concurso.

---

## 7. Cómo explicar el sistema en la presentación

Tres ideas que sostienen todo lo demás:

**1. El backend está en capas para que las reglas del negocio vivan en un solo lugar.**
Ejemplo: *«no puede haber dos ediciones abiertas»* se escribe una vez, en el servicio,
y sirve igual si mañana se invoca desde la web, desde un script o desde una app móvil.

**2. El frontend está organizado por módulo de negocio, no por tipo de archivo.**
Para trabajar en alfombras se abre una carpeta. Para dar de baja el módulo, se borra
una carpeta. Los módulos no se conocen entre sí.

**3. Lo compartido se extrajo de duplicación real, no se diseñó por adelantado.**
El mejor ejemplo es `LightboxImagen`: estaba escrito dos veces, se unificó, y hoy sirve
a tres consumidores distintos —galería pública, anuncios y moderación— sin un `if` de
dominio adentro.

### Si preguntan por validación

Está en **dos capas a propósito**, y las dos hacen falta:

- El navegador rechaza al instante lo que no corresponde (un video, una imagen de 7 MB)
  y muestra el motivo. Evita un viaje inútil al servidor.
- El backend vuelve a validar todo, porque el navegador se puede saltear. Pillow abre
  cada imagen antes de aceptarla, así que un video renombrado a `.jpg` se rechaza.

La autoridad es el backend; el frontend es comodidad.

### Si preguntan por seguridad

- JWT con renovación en cola: una sola renovación aunque fallen varias peticiones a la vez.
- Permisos por acción, no por vista: la misma clase atiende endpoints públicos y
  restringidos.
- Saneado de HTML por tipo de campo (`CleanCharField`, `RichTextField`), no por
  disciplina del programador.
- Lista blanca en búsquedas y ordenamientos: un query param no puede ordenar por
  cualquier columna.

### Si preguntan por la deuda técnica

Conviene adelantarse y mostrar que está identificada: 16 componentes huérfanos, una
página duplicada, una API sin consumidor y ausencia de tests. Tener el inventario
hecho demuestra control sobre el proyecto; que lo descubran ellos, no.

---

## Anexo — Mapa rápido de archivos

### Backend

```
backend/
├── config/settings/{base,development,production}.py   Configuración por entorno
├── config/urls.py                                     Montaje de las apps
├── utils/                                             Result, excepciones, mixins,
│                                                       paginación, query builder
└── apps/<app>/
    ├── models/           Estructura de datos
    ├── repositories/     Único acceso al ORM
    ├── services/         Reglas de negocio
    ├── serializers/      Validación y forma de entrada/salida
    ├── views/            Traducción HTTP
    └── urls.py           Rutas de la app
```

### Frontend

```
frontend/src/
├── main.js                  Arranque: Pinia, Router, Google Login
├── router/index.js          Composición de rutas + guard de /admin
├── middleware/auth.js       authGuard, adminGuard
├── services/api.js          Cliente HTTP único (JWT, FormData, refresh en cola)
├── stores/                  auth, ui
├── composables/             useToasts, useConfirm, useScrollReveal, useApi
├── helpers/                 apiError, dateUtils, mediaUrl, Pagination
├── layouts/                 AppLayout, AdmLayout, AuthLayout
├── components/              Publico/, Inputs/, Buttons/, Admin/
└── modules/<modulo>/
    ├── routes.js
    ├── service.js
    ├── composables/
    └── views/ + views/partes/
```
