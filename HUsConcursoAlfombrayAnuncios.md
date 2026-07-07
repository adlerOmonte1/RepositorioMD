# Historias de Usuario — Senior Burgos

---

## ALF-01 — Inscripción de una alfombra al concurso

| Identificador de la historia | ALF-01 | Nombre de la historia | Inscripción de una alfombra al concurso |
|---|---|---|---|

**Historia de Usuario**

Como participante, quiero inscribir una alfombra indicando título, descripción, lugar, nombre del autor y hasta 3 fotografías, para que sea evaluada dentro del concurso de la edición activa.

**Campos del formulario de inscripción**
- Título (obligatorio)
- Descripción (obligatorio)
- Lugar (obligatorio)
- Nombre del autor (opcional)
- Fotografías (mínimo 1, máximo 3)

**Criterio de aceptación**
- El sistema debe rechazar el registro si falta título, descripción o lugar, mostrando el mensaje "Debe completar los campos obligatorios".
- El sistema debe permitir adjuntar entre 1 y 3 fotografías; si el usuario intenta adjuntar una cuarta, debe bloquear la carga y mostrar "Solo se permiten hasta 3 fotografías por alfombra".
- Si el campo "nombre del autor" queda vacío, el sistema debe asumir como autor al usuario que realiza la inscripción.
- La alfombra debe quedar asociada automáticamente a la edición que esté activa en el momento de la inscripción.
- Si no existe ninguna edición activa, el sistema debe impedir la inscripción y mostrar "No hay una edición abierta actualmente".
  
**Requerimientos Funcionales:**
> RF01. El sistema debe permitir a un usuario inscribir una alfombra indicando título, descripción, lugar y nombre del autor, el cual puede ser distinto del usuario que realiza la publicación.
> 
> RF02. El sistema debe permitir adjuntar múltiples fotografías a cada alfombra inscrita pero máximo 3 imágenes.
---

## ALF-02 — Registro de alfombra en estado pendiente

| Identificador de la historia | ALF-02 | Nombre de la historia | Registro de alfombra en estado pendiente |
|---|---|---|---|

**Historia de Usuario**

Como sistema, debo mantener cada alfombra inscrita en estado "pendiente" y oculta del público hasta que un administrador la apruebe, para garantizar que solo contenido revisado se publique.

**Comportamiento y estados**
- Estado de la alfombra: pendiente / aprobada / rechazada

**Criterio de aceptación**
- Toda alfombra debe crearse con estado "pendiente" por defecto, sin excepción.
- Mientras el estado sea "pendiente" o "rechazada", la alfombra no debe aparecer en ningún listado público (galería, ranking).
- Si un usuario intenta acceder directamente al detalle de una alfombra que no está aprobada y no le pertenece, el sistema debe responder "Alfombra no disponible" en lugar de mostrar su contenido.
- El usuario que inscribió la alfombra sí debe poder consultar su propio estado (pendiente/aprobada/rechazada) desde su historial personal, aunque aún no esté aprobada.

**Requerimientos Funcionales:**
> RF03. El sistema debe registrar cada alfombra en estado "pendiente" hasta que un administrador la apruebe.
> 
> RF04. El sistema debe impedir que una alfombra no aprobada se muestre en la galería pública.
---

## ALF-03 — Aprobación o rechazo de una alfombra

| Identificador de la historia | ALF-03 | Nombre de la historia | Aprobación o rechazo de una alfombra |
|---|---|---|---|

**Historia de Usuario**

Como administrador, quiero aprobar o rechazar cada alfombra inscrita, para controlar qué contenido se publica dentro del concurso.

**Acciones disponibles para el administrador**
- Aprobar
- Rechazar

**Criterio de aceptación**
- Solo un usuario con rol "administrador" puede ejecutar la acción de aprobar o rechazar; si un usuario con otro rol lo intenta, el sistema debe responder "Acción no autorizada".
- Al aprobar, el estado debe cambiar a "aprobada" y la alfombra debe pasar a ser visible en la galería pública de forma inmediata.
- Al rechazar, el estado debe cambiar a "rechazada" y la alfombra debe permanecer oculta del público hasta que el administrador la revierta explícitamente.
- El sistema no debe permitir que una alfombra rechazada vuelva a "aprobada" automáticamente; el cambio debe ser una acción explícita del administrador.

**Requerimientos Funcionales:**
> RF05. El administrador debe poder aprobar o rechazar cada alfombra inscrita.

---

## ALF-04 — Visualización de galería pública de alfombras aprobadas

| Identificador de la historia | ALF-04 | Nombre de la historia | Visualización de galería pública de alfombras aprobadas |
|---|---|---|---|


**Historia de Usuario**

Como visitante, quiero ver en una galería pública todas las alfombras aprobadas junto con sus fotografías, para conocer a las participantes del concurso.

**Campos visibles en la galería pública**
- Título, descripción, lugar, nombre del autor, fotografías

**Criterio de aceptación**
- La galería debe mostrar únicamente alfombras en estado "aprobada"; ninguna alfombra pendiente o rechazada debe aparecer bajo ninguna condición.
- Si una alfombra aprobada no tiene ninguna fotografía cargada, el sistema debe mostrarla igual, indicando "Sin fotografías disponibles".
- La galería debe filtrarse por la edición activa de forma predeterminada, permitiendo consultar ediciones anteriores solo si el visitante lo solicita explícitamente.

**Requerimientos Funcionales:**
> RF06. El sistema debe mostrar en una galería pública todas las alfombras aprobadas junto con sus fotografías.
---

## ALF-05 — Calificación y "me gusta" a una alfombra

| Identificador de la historia | ALF-05 | Nombre de la historia | Calificación y "me gusta" a una alfombra |
|---|---|---|---|

**Historia de Usuario**

Como usuario, quiero calificar y dar "me gusta" a las alfombras publicadas, para expresar mi opinión sobre cuáles son mis favoritas.

**Campos y acciones de calificación**
- Calificación
- Me gusta (sí/no)

**Criterio de aceptación**
- El sistema debe impedir que un mismo usuario registre más de una calificación para la misma alfombra; si lo intenta, debe mostrar "Ya calificaste esta alfombra" y no debe crear un segundo registro.
- Solo se debe permitir calificar alfombras en estado "aprobada"; si el usuario intenta calificar una pendiente o rechazada, el sistema debe responder "No se puede calificar una alfombra no aprobada".
- El sistema debe registrar la calificación y el "me gusta" como parte del mismo registro por usuario y alfombra, no como acciones separadas que puedan duplicarse.
- Un usuario no debe poder calificar su propia alfombra.

**Requerimientos Funcionales:**
> RF07. El sistema debe permitir a los usuarios calificar y dar "me gusta" a las alfombras publicadas.
> 
> RF08. El sistema debe impedir que un mismo usuario califique más de una vez la misma alfombra.

---

## ALF-06 — Ranking de alfombras por calificación

| Identificador de la historia | ALF-06 | Nombre de la historia | Ranking de alfombras por calificación |
|---|---|---|---|


**Historia de Usuario**

Como visitante, quiero ver un ranking de alfombras basado en las calificaciones y "me gusta" recibidos, para saber cuáles son las mejores de la edición.

**Campos visibles en el ranking**
- Posición, título de la alfombra, autor, calificación promedio, cantidad de "me gusta"

**Criterio de aceptación**
- El ranking debe incluir únicamente alfombras en estado "aprobada".
- El sistema debe definir un criterio de desempate explícito (por ejemplo, mayor cantidad de "me gusta") cuando dos alfombras tengan la misma calificación promedio.
- El ranking debe poder consultarse filtrado por la edición activa, y también por ediciones anteriores si el visitante lo solicita.
- Si ninguna alfombra ha sido calificada aún en la edición activa, el sistema debe mostrar "Aún no hay calificaciones registradas para esta edición" en lugar de una lista vacía sin explicación.

**Requerimientos Funcionales:**
> RF09. El sistema debe generar un ranking de alfombras en base a las calificaciones y "me gusta" recibidos, para que los espectadores vean cuáles son las mejores.
---

## GAL-01 — Publicación de fotografía en la galería general

| Identificador de la historia | GAL-01 | Nombre de la historia | Publicación de fotografía en la galería general |
|---|---|---|---|


**Historia de Usuario**

Como usuario, quiero subir fotografías a una galería general, con autor y descripción, para compartir imágenes de la fiesta sin necesidad de inscribir una alfombra en el concurso.

**Campos del formulario de publicación**
- Fotografía (obligatoria)
- Autor (opcional)
- Descripción (opcional)

**Criterio de aceptación**
- El sistema debe permitir la publicación sin pasar por ningún proceso de aprobación administrativa, a diferencia del concurso de alfombras.
- El sistema debe rechazar la publicación si no se adjunta ninguna fotografía, mostrando "Debe adjuntar una fotografía".
- Si el usuario no ingresa autor ni descripción, el sistema debe permitir publicar igual, dejando esos campos vacíos en la vista pública.
- Las fotografías deben mostrarse ordenadas de la más reciente a la más antigua.

**Requerimientos Funcionales:**
> RF10. El sistema debe permitir a los usuarios subir fotografías a una galería general, independiente de las alfombras inscritas en el concurso.
> 
> RF11. El sistema debe permitir asociar autor y descripción a cada fotografía de la galería general.
---

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

---

## ANU-01 — Publicación de un anuncio por el administrador

| Identificador de la historia | ANU-01 | Nombre de la historia | Publicación de un anuncio por el administrador |
|---|---|---|---|


**Historia de Usuario**

Como administrador, quiero publicar un anuncio con título, contenido y fecha, para informar a los usuarios sobre novedades relacionadas con la fiesta.

**Campos del formulario de publicación**
- Título (obligatorio)
- Contenido (obligatorio)
- Imagen (opcional)
- Fecha de publicación (automática)

**Criterio de aceptación**
- Solo un usuario con rol "administrador" puede publicar un anuncio; si otro rol lo intenta, el sistema debe responder "Acción no autorizada".
- El sistema debe rechazar la publicación si falta el título o el contenido, mostrando "Debe completar los campos obligatorios".
- La fecha de publicación debe registrarse automáticamente al momento de guardar, sin que el administrador pueda modificarla manualmente al crear el anuncio.
- Todo anuncio publicado debe quedar visible de inmediato en el frontend, sin pasos adicionales de aprobación.

**Requerimientos Funcionales:**
> RF27. El sistema debe permitir al administrador publicar anuncios (texto, y opcionalmente imagen) para informar a los usuarios.
---

## ANU-02 — Visualización de anuncios en el frontend

| Identificador de la historia | ANU-02 | Nombre de la historia | Visualización de anuncios en el frontend |
|---|---|---|---|


**Historia de Usuario**

Como visitante, quiero ver los anuncios publicados por el administrador en el frontend, para estar informado de las novedades de la fiesta.

**Campos visibles del anuncio**
- Título, contenido, imagen (si tiene), fecha de publicación

**Criterio de aceptación**
- El sistema debe mostrar únicamente los anuncios marcados como activos; los anuncios eliminados o desactivados no deben mostrarse bajo ninguna condición.
- Los anuncios deben mostrarse ordenados de la fecha de publicación más reciente a la más antigua.
- Si no existe ningún anuncio activo, el sistema debe mostrar "No hay anuncios disponibles por el momento" en lugar de una sección vacía sin explicación.

**Requerimientos Funcionales:**
> RF28. El sistema debe mostrar los anuncios publicados en el frontend para que cualquier visitante los pueda ver.
---

## ANU-03 — Edición o eliminación de un anuncio

| Identificador de la historia | ANU-03 | Nombre de la historia | Edición o eliminación de un anuncio |
|---|---|---|---|


**Historia de Usuario**

Como administrador, quiero editar o eliminar un anuncio ya publicado, para corregir información desactualizada o retirar contenido que ya no aplica.

**Acciones disponibles para el administrador**
- Editar
- Eliminar

**Criterio de aceptación**
- Solo un usuario con rol "administrador" puede editar o eliminar un anuncio; si otro rol lo intenta, el sistema debe responder "Acción no autorizada".
- Al editar, el sistema debe actualizar el contenido existente sin generar un nuevo registro ni duplicar el anuncio.
- Al eliminar, el sistema debe ocultar el anuncio del frontend de inmediato; se recomienda una eliminación lógica (marcar como inactivo) en vez de borrarlo físicamente, para conservar el historial administrativo.
- Si el administrador intenta editar o eliminar un anuncio que ya no existe (por ejemplo, eliminado por otro administrador en paralelo), el sistema debe mostrar "El anuncio ya no está disponible".

**Requerimientos Funcionales:**
> RF29. El administrador debe poder editar o eliminar un anuncio publicado.
