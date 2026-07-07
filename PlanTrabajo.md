# Plan de Trabajo — Equipo de 4 Desarrolladores (Django + Vue)

Plan preliminar para resolver las 24 HUs del proyecto (Autenticación, Alfombras, Galería, Dinámica, Ediciones, Anuncios) con un equipo de 4 desarrolladores fullstack junior, usando **Django (backend)** + **Vue (frontend)**, aplicando **SOLID**, y apoyándose en IA en cada etapa.

**Prioridad que pediste, y que se respeta en todo el plan:** primero que el sistema funcione bien y esté optimizado (backend y frontend verificados end-to-end) — el estilo final, animaciones y pulido visual van al final, no antes.

---

## 0. Enfoque general

- **Divide por módulo, no por capa.** Cada dev hace backend + frontend de su módulo (vertical, no "uno hace todo el backend y otro todo el frontend"), porque son fullstack y así cada uno entiende el flujo completo de su parte.
- **Nadie empieza un módulo sin que su contrato esté definido.** Antes de repartir Alfombras/Dinámica/Galería/Anuncios, el equipo define juntos: formato estándar de respuesta de la API, manejo de errores, y la interfaz común de "Dinámica" (para aplicar Abierto/Cerrado).
- **Nada se "ve bonito" hasta que funciona.** Las Fases 1 y 2 se hacen con estilos mínimos (Bootstrap/Tailwind sin personalizar). El estilo final es la Fase 4, no antes.

---

## 1. Arquitectura base (para que SOLID no se quede solo en el papel)

### Backend (Django + Django REST Framework)

- **Un app de Django por módulo**: `authentication`, `ediciones`, `alfombras`, `galeria`, `dinamica`, `anuncios`. Esto ya es Responsabilidad Única a nivel de proyecto.
- **Capa de servicios** (`services.py` dentro de cada app): la lógica de negocio (validar, calcular, decidir) vive ahí, no en las vistas. Las vistas (`views.py`) solo reciben la petición, llaman al servicio y devuelven la respuesta. Esto hace que la lógica se pueda probar sin levantar un servidor HTTP.
- **Interfaz común para Dinámica** (`dinamica/interfaces.py`), usando `abc.ABC`:
  ```python
  class IDinamica(ABC):
      @abstractmethod
      def registrar_intento(self, usuario, datos): ...
      @abstractmethod
      def calcular_puntaje(self, datos): ...
      @abstractmethod
      def obtener_ranking(self, edicion=None): ...
  ```
  `KahootService` la implementa. Si más adelante vuelve Memorama o QR, se crea una clase nueva que implemente `IDinamica` sin tocar `KahootService`.

### Frontend (Vue)

- **Arquitectura por features**, no por tipo de archivo:
  ```
  src/features/alfombras/
    components/
    views/
    store.js        (Pinia)
    api.js           (llamadas a la API de este módulo)
  ```
- **Un cliente HTTP centralizado** (`src/services/http.js`) que maneja el token de autenticación y los errores de forma uniforme, para que ningún módulo tenga que repetir esa lógica.
- **Componentes compartidos** (`src/components/ui/`): botones, inputs, cards, modales, mensajes de error/vacío — reutilizados por todos los módulos desde el día uno, aunque sin estilo final todavía.

---

## 2. Fases del proyecto

### Fase 0 — Preparación (todo el equipo, junto)

- [ ] Crear repositorio, definir convención de ramas (`feature/ALF-01-inscripcion`) y de commits.
- [ ] Levantar el proyecto Django (apps vacías por módulo) y el proyecto Vue (estructura de carpetas por feature).
- [ ] Configurar base de datos (usar el esquema MySQL ya definido, agregando la tabla de autenticación).
- [ ] Definir el formato estándar de respuesta de la API (éxito y error) y los códigos de estado que se van a usar.
- [ ] Definir la interfaz `IDinamica` en conjunto, antes de repartir Dinámica.
- [ ] Configurar linters (`flake8`/`black` en backend, `eslint`/`prettier` en frontend) para que el código de los 4 quede parejo.

### Fase 1 — Núcleo (bloqueante, en pareja para no trabarse solos)

| Quién | Módulo | HUs |
|---|---|---|
| Dev A + Dev B | Autenticación | AUTH-01 a AUTH-04 |
| Dev C + Dev D | Ediciones | EDI-01 a EDI-05 |

Todo lo demás depende de que estos dos módulos existan (los usuarios necesitan poder loguearse, y las alfombras/Kahoot necesitan una edición activa donde registrarse).

### Fase 2 — Módulos en paralelo (cada dev, su módulo completo)

| Quién | Módulo | HUs | Depende de |
|---|---|---|---|
| Dev A | Concurso de Alfombras | ALF-01 a ALF-06 | Autenticación, Ediciones |
| Dev B | Dinámica (Kahoot) | DIN-01 a DIN-04 | Autenticación, Ediciones |
| Dev C | Galería general | GAL-01, GAL-02 | Autenticación |
| Dev D | Anuncios | ANU-01 a ANU-03 | Autenticación |

Alfombras es el módulo más grande (6 HUs con más reglas de negocio: aprobación, calificación, ranking); si Dev A se atrasa, Dev C o D pueden apoyar en ALF-05/ALF-06 una vez terminen su propio módulo, ya que Galería y Anuncios son más cortos.

### Fase 3 — Integración y verificación funcional (todo el equipo)

Aquí es donde se cumple lo que pediste: comprobar que backend y frontend funcionan bien juntos, **antes** de tocar estilos.

- [ ] Conectar el enrutamiento global del frontend (todas las vistas navegables desde un layout común).
- [ ] Verificar que los permisos por rol (administrador/participante) se respeten en cada módulo, no solo en el propio.
- [ ] Revisión cruzada de código: cada dev revisa el módulo de otro (no el propio), buscando específicamente que se cumplan los criterios de aceptación de las HUs.
- [ ] Probar manualmente cada HU contra su criterio de aceptación tal como está escrito en el documento de historias.
- [ ] Revisar rendimiento básico: evitar consultas N+1 en Django (usar `select_related`/`prefetch_related`), paginar listados largos (galería, ranking).
- [ ] Congelar el "checklist de sistema funcional" (ver sección 3) antes de pasar a la Fase 4.

### Fase 4 — Estilo final, animaciones y pulido

- [ ] Definir sistema de diseño definitivo (colores, tipografía, espaciados) sobre los componentes compartidos ya existentes.
- [ ] Aplicar animaciones/transiciones (carga de galería, cambios de estado, notificaciones).
- [ ] Responsive (mobile-first, pensando en que el QR y el Kahoot se van a usar mucho desde el celular).
- [ ] Pulir mensajes de error y estados vacíos (que ya existen funcionalmente desde la Fase 2, aquí solo se les da forma visual).
- [ ] Accesibilidad básica (contraste, tamaños de texto, foco de teclado).

### Fase 5 — Despliegue y documentación (opcional, si el tiempo lo permite)

- [ ] Documentar la API (Swagger/OpenAPI, generado automáticamente por DRF).
- [ ] Preparar variables de entorno para producción.
- [ ] Desplegar backend y frontend.

---

## 3. Checklist reutilizable para abordar cualquier HU

Este es el mismo checklist para las 24 HUs, sin importar el módulo — así los 4 devs trabajan de forma consistente:

**Backend**
1. Modelo (si no existe ya en el esquema).
2. Serializer.
3. Servicio (`services.py`) con la lógica de negocio, siguiendo el criterio de aceptación de la HU punto por punto.
4. Vista/Viewset (delgada, solo llama al servicio).
5. Prueba unitaria del servicio (usar el criterio de aceptación como lista de casos de prueba).

**Frontend**
6. Llamada a la API en `api.js` del feature.
7. Estado en el store (Pinia) si aplica.
8. Componente/vista, primero sin estilo (HTML semántico + clases mínimas).
9. Manejo de los mensajes de error/vacío que pide el criterio de aceptación (aunque sea con estilo genérico por ahora).

**Cierre**
10. Probar la HU completa contra su criterio de aceptación tal cual está redactado.
11. Pull request, revisado por otro dev del equipo.

---

## 4. Cómo aprovechar la IA en cada paso (siendo junior)

- **Para generar el esqueleto**, no la decisión final: pide a la IA que arme el modelo/serializer/viewset a partir del criterio de aceptación de la HU, pero revisa que las validaciones realmente coincidan con lo que dice la ficha.
- **Para convertir criterios de aceptación en pruebas**: cada bullet de "Criterio de aceptación" ya está casi listo para ser un caso de prueba (`test_...`). Pide a la IA que te ayude a traducir cada bullet en una prueba concreta.
- **Para revisar SOLID**: pégale a la IA un archivo (`services.py`, un componente Vue) y pregúntale específicamente "¿esta clase tiene más de una responsabilidad?" o "¿esto rompe Abierto/Cerrado si mañana agrego un juego nuevo?" — es más útil que pedir una revisión genérica.
- **Para entender errores**: en vez de solo copiar el traceback y pedir el arreglo, pide también que te explique *por qué* pasó, para no repetir el mismo error en otra HU.
- **Para el frontend**: describe el componente en términos de la HU ("necesito un formulario con estos campos, estas validaciones, estos mensajes de error") en vez de pedir "hazme un formulario bonito" — el estilo llega en la Fase 4.

---

## 5. Flujo de trabajo en Git (equipo pequeño, mantenerlo simple)

- Rama por HU: `feature/ALF-01-inscripcion-alfombra`.
- Commits pequeños y descriptivos, referenciando el ID de la HU.
- Pull request obligatorio antes de fusionar a `main`/`develop`, revisado por al menos un compañero.
- Nadie fusiona su propio código sin que otro lo haya revisado — con 4 personas esto es fácil de sostener y ayuda a que los 4 conozcan todo el sistema, no solo su módulo.

---

## 6. Tablero sugerido (Kanban)

```
Por hacer → Backend en progreso → Frontend en progreso → En revisión (PR) → Probado (funcional, sin estilo) → Con estilo final → Terminado
```

La columna **"Probado (funcional, sin estilo)"** es clave: es el punto donde el equipo confirma que una HU cumple su criterio de aceptación antes de invertir tiempo en verse bien. Nada debería saltarse esa columna.