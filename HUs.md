## GAL-02 — Detalle de autor y descripción en fotografías de la galería general

| Identificador de la historia | GAL-02 | Nombre de la historia | Detalle de autor y descripción en fotografías de la galería general |
|---|---|---|---|

**Historia de Usuario**

Como usuario que sube contenido, quiero asociar obligatoriamente un autor y una descripción a cada fotografía que publico en la galería general, para que los visitantes conozcan el contexto y el crédito de la imagen.

**Campos del formulario de publicación**
- Fotografía (obligatorio)
- Autor (obligatorio)
- Descripción (obligatorio)

**Criterio de aceptación**
- El sistema debe rechazar el registro de la fotografía si los campos "Autor" o "Descripción" se envían vacíos, mostrando el mensaje "Debe completar los campos obligatorios de la fotografía".
- En la interfaz de la galería general pública, cada tarjeta de fotografía debe renderizar de forma visible y legible el nombre del autor y el texto de la descripción asociada.
- Si el usuario que sube la foto escribe un autor definitivo distinto a su nombre de cuenta, el sistema debe registrar y mostrar el nombre literal ingresado en el campo "Autor".

**Requerimientos Funcionales:**
> RF11. El sistema debe permitir asociar autor y descripción a cada fotografía de la galería general.

---

## DIN-01 — Gestión del banco de preguntas para la dinámica

| Identificador de la historia | DIN-01 | Nombre de la historia | Gestión del banco de preguntas para la dinámica |
|---|---|---|---|

**Historia de Usuario**

Como administrador, quiero registrar preguntas de opción múltiple con exactamente cuatro alternativas, una respuesta correcta y una explicación opcional, para construir el banco de trivia del juego de la fiesta.

**Campos del formulario de preguntas**
- Enunciado de la pregunta (obligatorio)
- Alternativa A, B, C y D (obligatorias)
- Alternativa Correcta (selección obligatoria entre A, B, C o D)
- Explicación de la respuesta (opcional)

**Criterio de aceptación**
- El sistema debe validar que las cuatro alternativas contengan texto; si alguna queda vacía, debe impedir el guardado y mostrar "Debe rellenar las cuatro alternativas".
- El administrador debe marcar obligatoriamente una (y solo una) de las alternativas como la respuesta correcta mediante un control de selección (radio button o similar).
- Si el campo "Explicación" se deja vacío, el sistema debe permitir el guardado y omitir dicho bloque de texto cuando la pregunta sea resuelta por los usuarios en el juego.

**Requerimientos Funcionales:**
> RF12. El sistema debe contar con un banco de preguntas de opción múltiple (cuatro alternativas), cada una con una respuesta correcta y explicación opcional.

---

## DIN-02 — Respuestas en modalidad tipo Kahoot con registro de puntaje y tiempo

| Identificador de la historia | DIN-02 | Nombre de la historia | Respuestas en modalidad tipo Kahoot con registro de puntaje y tiempo |
|---|---|---|---|

**Historia de Usuario**

Como usuario participante, quiero responder las preguntas de la dinámica visualizando un temporizador en cuenta regresiva y recibiendo puntos proporucionales a mi velocidad de respuesta, para competir interactivamente en la trivia.

**Campos y telemetría del juego**
- Tiempo límite por pregunta (en segundos)
- Tiempo de respuesta consumido (en milisegundos)
- Puntaje obtenido

**Criterio de aceptación**
- Al cargar una pregunta, el sistema debe iniciar un temporizador regresivo visible en el frontend. Si el tiempo llega a cero antes de recibir una entrada, la pregunta se marca automáticamente como "Incorrecta" con 0 puntos.
- Si el usuario selecciona la respuesta correcta, el sistema debe calcular el puntaje asignando un valor máximo por responder instantáneamente, disminuyendo de forma proporcional por cada milisegundo que el usuario demoró en presionar la alternativa.
- Si el usuario selecciona una respuesta incorrecta, el sistema otorga de forma inmediata 0 puntos, sin importar el tiempo empleado.
- El sistema debe persistir en la base de datos el ID del usuario, el ID de la pregunta, el puntaje obtenido y el tiempo exacto que le tomó responder.

**Requerimientos Funcionales:**
> RF13. El sistema debe permitir a un usuario responder preguntas en modalidad tipo Kahoot, registrando puntaje y tiempo de respuesta.

---

## DIN-03 — Restricción de intentos duplicados en la trivia Kahoot

| Identificador de la historia | DIN-03 | Nombre de la historia | Restricción de intentos duplicados en la trivia Kahoot |
|---|---|---|---|

**Historia de Usuario**

Como sistema, debo bloquear cualquier intento de un usuario de responder una misma pregunta más de una vez dentro de la edición del juego, para mantener la transparencia y equidad de la competencia.

**Comportamiento y estados**
- Estado del intento: Completado / Bloqueado

**Criterio de aceptación**
- Antes de renderizar una pregunta a un usuario en el modo de juego Kahoot, el sistema debe verificar en la base de datos si ya existe un registro de respuesta de ese usuario para esa pregunta en la edición activa.
- Si ya existe un registro, el sistema debe impedir el acceso a las alternativas de respuesta y mostrar el mensaje "Ya has respondido esta pregunta en esta edición del juego".
- En caso de que el usuario intente manipular las peticiones HTTP del frontend enviando una segunda respuesta por software, la API del backend debe rechazar la transacción devolviendo un código de error de solicitud inválida.

**Requerimientos Funcionales:**
> RF14. El sistema debe impedir que un usuario responda la misma pregunta más de una vez dentro del juego Kahoot.

---

## DIN-04 — Clasificación y ranking de resultados del juego Kahoot

| Identificador de la historia | DIN-04 | Nombre de la historia | Clasificación y ranking de resultados del juego Kahoot |
|---|---|---|---|

**Historia de Usuario**

Como visitante de la aplicación, quiero ver una tabla de posiciones que ordene a los jugadores de mayor a menor puntaje acumulado en la trivia, para identificar a los líderes del juego.

**Campos visibles en el ranking**
- Posición en la tabla, nombre de usuario, puntaje total acumulado, tiempo total acumulado

**Criterio de aceptación**
- El ranking debe calcularse sumando de forma dinámica todos los puntajes de las preguntas válidas respondidas por cada usuario en la edición en curso.
- El ordenamiento de la lista debe realizarse de forma descendente en función al "puntaje total acumulado".
- En caso de que dos o más usuarios empaten con el mismo puntaje exacto, el sistema aplicará un criterio de desempate automático posicionando primero al usuario que registre el menor "tiempo total acumulado" de respuesta en sus preguntas correctas.

**Requerimientos Funcionales:**
> RF15. El sistema debe generar un ranking de resultados del juego Kahoot.

---

## EDI-01 — Organización y segmentación de datos por edición anual

| Identificador de la historia | EDI-01 | Nombre de la historia | Organización y segmentación de datos por edición anual |
|---|---|---|---|

**Historia de Usuario**

Como sistema, debo encapsular, relacionar y segmentar lógicamente toda la información operativa (inscripciones de alfombras, puntajes de calificaciones, respuestas y rankings de juegos) bajo la clave de la edición correspondiente, para evitar el cruce de datos entre diferentes años.

**Estructura y relaciones de datos**
- Llave foránea: ID_Edicion (presente en Alfombras, Calificaciones, Intentos_Trivia, Ganadores)

**Criterio de aceptación**
- Toda consulta realizada en el frontend (galerías de alfombras, ranking de votos o líderes de Kahoot) debe incluir de manera implícita una condición de filtrado por el identificador de la edición que se esté visualizando.
- El sistema no debe permitir bajo ninguna circunstancia que los puntajes o las interacciones efectuadas en un año afecten las métricas, promedios o clasificaciones de ediciones pasadas o futuras.

**Requerimientos Funcionales:**
> RF22. El sistema debe organizar toda la información como alfombras, calificaciones y resultados de juegos según la edición a la que pertenece.

---

## EDI-02 — Apertura de una nueva edición anual por el administrador

| Identificador de la historia | EDI-02 | Nombre de la historia | Apertura de una nueva edición anual por el administrador |
|---|---|---|---|

**Historia de Usuario**

Como administrador, quiero registrar y abrir una nueva edición del sistema al dar inicio a la festividad de cada año, para habilitar los formularios de interacción y reiniciar los tableros del concurso.

**Campos del formulario de edición**
- Nombre de la edición (ej. "Edición XLIV - 2026") (obligatorio)
- Año correspondiente (obligatorio)
- Estado (Generado automáticamente como "Activa")

**Criterio de aceptación**
- Solo un usuario con rol "administrador" tiene permisos para crear ediciones; si otro rol lo intenta, el sistema responde "Acción no autorizada".
- El sistema debe validar que no exista otra edición en estado "Activa" de forma simultánea. Si se detecta una edición abierta, se bloquea el proceso de guardado mostrando el mensaje "Debe cerrar la edición actual antes de abrir una nueva".
- Al consolidarse la creación exitosa, el sistema asume esta nueva edición como el contexto predeterminado global de la aplicación.

**Requerimientos Funcionales:**
> RF23. El administrador debe poder crear una nueva edición al iniciar la fiesta de cada año.

---

## EDI-03 — Cierre de la edición anual en curso

| Identificador de la historia | EDI-03 | Nombre de la historia | Cierre de la edición anual en curso |
|---|---|---|---|

**Historia de Usuario**

Como administrador, quiero modificar el estado de la edición activa a "Cerrada" una vez concluido el periodo festivo del año, para congelar la información y dar por finalizado el concurso y las trivias.

**Acciones disponibles para el administrador**
- Cambiar estado a "Cerrada"

**Criterio de aceptación**
- Al ejecutar el cierre, el estado de la edición cambia irreversiblemente a "Cerrada" o "Histórica".
- A partir de ese instante, el backend debe rechazar cualquier intento de inscripción de alfombras, envíos de calificaciones ("me gusta") o respuestas en la trivia para dicha edición, respondiendo "El periodo de participación para esta edición ha finalizado".
- El contenido de la edición cerrada pasa automáticamente a formar parte exclusiva de los módulos de consulta histórica del sistema.

**Requerimientos Funcionales:**
> RF24. El sistema debe permitir cerrar una edición al finalizar el periodo correspondiente.

---

## EDI-04 — Consulta del historial de ediciones anteriores

| Identificador de la historia | EDI-04 | Nombre de la historia | Consulta del historial de ediciones anteriores |
|---|---|---|---|

**Historia de Usuario**

Como visitante de la plataforma, quiero disponer de un menú selector de años para navegar y consultar los datos archivados de festividades previas, para apreciar retrospectivamente los eventos pasados.

**Componentes de la interfaz**
- Control desplegable (Combo-box o Línea de tiempo) con el listado de ediciones cerradas.

**Criterio de aceptación**
- El sistema debe renderizar públicamente una lista de todas las ediciones cuyo estado sea "Cerrada".
- Al seleccionar una edición histórica del listado, el frontend debe recargar las vistas de la galería de alfombras y los rankings de votación extrayendo únicamente los registros enlazados con el ID de la edición seleccionada.
- Si una edición seleccionada carece de registros de alfombras guardados, la interfaz debe mostrar el mensaje informático "No existen alfombras registradas en este año".

**Requerimientos Funcionales:**
> RF25. El sistema debe permitir consultar el historial de ediciones anteriores.

---

## EDI-05 — Exposición pública del registro histórico de ganadores

| Identificador de la historia | EDI-05 | Nombre de la historia | Exposición pública del registro histórico de ganadores |
|---|---|---|---|

**Historia de Usuario**

Como visitante de la aplicación, quiero visualizar un cuadro de honor permanente que liste explícitamente a los ganadores del concurso de alfombras y de la trivia Kahoot de cada año, para preservar la memoria histórica de la festividad.

**Campos visibles en el cuadro de honor**
- Nombre de la edición, Categoría (Alfombras / Kahoot), Primer Puesto (Nombre del autor/usuario e imagen/puntaje), Segundo Puesto, Tercer Puesto.

**Criterio de aceptación**
- El sistema debe contar con una sección pública denominada "Cuadro de Honor" o "Ganadores Históricos" accesible desde el menú de navegación principal sin requerir inicio de sesión.
- Esta sección presentará bloques organizados cronológicamente (del año más reciente al más antiguo), detallando los tres primeros puestos de cada edición cerrada.
- Los datos reflejados en esta pantalla deben ser de solo lectura y el sistema velará por su persistencia inalterable a lo largo del tiempo.

**Requerimientos Funcionales:**
> RF26. El sistema debe mostrar públicamente el listado de ganadores de cada edición, para conservar el registro histórico de la fiesta con el paso de los años.