# Historias de Usuario Detalladas — Alfombras, Galería y Anuncios

Formato de ficha por historia: identificador, RF relacionado (tal cual fue redactado), historia de usuario, campos/acciones involucradas, y criterios de aceptación enfocados en comportamiento funcional (no en diseño visual).

---

## ALF-01 — Inscripción de una alfombra al concurso

| Identificador de la historia | ALF-01 | Nombre de la historia | Inscripción de una alfombra al concurso |
|---|---|---|---|

**RF relacionado (tal cual):**
> RF01. El sistema debe permitir a un usuario inscribir una alfombra indicando título, descripción, lugar y nombre del autor, el cual puede ser distinto del usuario que realiza la publicación.
> RF02. El sistema debe permitir adjuntar múltiples fotografías a cada alfombra inscrita pero máximo 3 imágenes.

**Historia**
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

---

## ALF-02 — Registro de alfombra en estado pendiente

| Identificador de la historia | ALF-02 | Nombre de la historia | Registro de alfombra en estado pendiente |
|---|---|---|---|

**RF relacionado (tal cual):**
> RF03. El sistema debe registrar cada alfombra en estado "pendiente" hasta que un administrador la apruebe.
> RF04. El sistema debe impedir que una alfombra no aprobada se muestre en la galería pública.

**Historia**
Como sistema, debo mantener cada alfombra inscrita en estado "pendiente" y oculta del público hasta que un administrador la apruebe, para garantizar que solo contenido revisado se publique.

**Comportamiento y estados**
- Estado de la alfombra: pendiente / aprobada / rechazada

**Criterio de aceptación**
- Toda alfombra debe crearse con estado "pendiente" por defecto, sin excepción.
- Mientras el estado sea "pendiente" o "rechazada", la alfombra no debe aparecer en ningún listado público (galería, ranking).
- Si un usuario intenta acceder directamente al detalle de una alfombra que no está aprobada y no le pertenece, el sistema debe responder "Alfombra no disponible" en lugar de mostrar su contenido.
- El usuario que inscribió la alfombra sí debe poder consultar su propio estado (pendiente/aprobada/rechazada) desde su historial personal, aunque aún no esté aprobada.

---

## ALF-03 — Aprobación o rechazo de una alfombra

| Identificador de la historia | ALF-03 | Nombre de la historia | Aprobación o rechazo de una alfombra |
|---|---|---|---|

**RF relacionado (tal cual):**
> RF05. El administrador debe poder aprobar o rechazar cada alfombra inscrita.

**Historia**
Como administrador, quiero aprobar o rechazar cada alfombra inscrita, para controlar qué contenido se publica dentro del concurso.

**Acciones disponibles para el administrador**
- Aprobar
- Rechazar

**Criterio de aceptación**
- Solo un usuario con rol "administrador" puede ejecutar la acción de aprobar o rechazar; si un usuario con otro rol lo intenta, el sistema debe responder "Acción no autorizada".
- Al aprobar, el estado debe cambiar a "aprobada" y la alfombra debe pasar a ser visible en la galería pública de forma inmediata.
- Al rechazar, el estado debe cambiar a "rechazada" y la alfombra debe permanecer oculta del público hasta que el administrador la revierta explícitamente.
- El sistema no debe permitir que una alfombra rechazada vuelva a "aprobada" automáticamente; el cambio debe ser una acción explícita del administrador.

---

## ALF-04 — Visualización de galería pública de alfombras aprobadas

| Identificador de la historia | ALF-04 | Nombre de la historia | Visualización de galería pública de alfombras aprobadas |
|---|---|---|---|

**RF relacionado (tal cual):**
> RF06. El sistema debe mostrar en una galería pública todas las alfombras aprobadas junto con sus fotografías.

**Historia**
Como visitante, quiero ver en una galería pública todas las alfombras aprobadas junto con sus fotografías, para conocer a las participantes del concurso.

**Campos visibles en la galería pública**
- Título, descripción, lugar, nombre del autor, fotografías

**Criterio de aceptación**
- La galería debe mostrar únicamente alfombras en estado "aprobada"; ninguna alfombra pendiente o rechazada debe aparecer bajo ninguna condición.
- Si una alfombra aprobada no tiene ninguna fotografía cargada, el sistema debe mostrarla igual, indicando "Sin fotografías disponibles".
- La galería debe filtrarse por la edición activa de forma predeterminada, permitiendo consultar ediciones anteriores solo si el visitante lo solicita explícitamente.

---

## ALF-05 — Calificación y "me gusta" a una alfombra

| Identificador de la historia | ALF-05 | Nombre de la historia | Calificación y "me gusta" a una alfombra |
|---|---|---|---|

**RF relacionado (tal cual):**
> RF07. El sistema debe permitir a los usuarios calificar y dar "me gusta" a las alfombras publicadas.
> RF08. El sistema debe impedir que un mismo usuario califique más de una vez la misma alfombra.

**Historia**
Como usuario, quiero calificar y dar "me gusta" a las alfombras publicadas, para expresar mi opinión sobre cuáles son mis favoritas.

**Campos y acciones de calificación**
- Calificación
- Me gusta (sí/no)

**Criterio de aceptación**
- El sistema debe impedir que un mismo usuario registre más de una calificación para la misma alfombra; si lo intenta, debe mostrar "Ya calificaste esta alfombra" y no debe crear un segundo registro.
- Solo se debe permitir calificar alfombras en estado "aprobada"; si el usuario intenta calificar una pendiente o rechazada, el sistema debe responder "No se puede calificar una alfombra no aprobada".
- El sistema debe registrar la calificación y el "me gusta" como parte del mismo registro por usuario y alfombra, no como acciones separadas que puedan duplicarse.
- Un usuario no debe poder calificar su propia alfombra.

---

## ALF-06 — Ranking de alfombras por calificación

| Identificador de la historia | ALF-06 | Nombre de la historia | Ranking de alfombras por calificación |
|---|---|---|---|

**RF relacionado (tal cual):**
> RF09. El sistema debe generar un ranking de alfombras en base a las calificaciones y "me gusta" recibidos, para que los espectadores vean cuáles son las mejores.

**Historia**
Como visitante, quiero ver un ranking de alfombras basado en las calificaciones y "me gusta" recibidos, para saber cuáles son las mejores de la edición.

**Campos visibles en el ranking**
- Posición, título de la alfombra, autor, calificación promedio, cantidad de "me gusta"

**Criterio de aceptación**
- El ranking debe incluir únicamente alfombras en estado "aprobada".
- El sistema debe definir un criterio de desempate explícito (por ejemplo, mayor cantidad de "me gusta") cuando dos alfombras tengan la misma calificación promedio.
- El ranking debe poder consultarse filtrado por la edición activa, y también por ediciones anteriores si el visitante lo solicita.
- Si ninguna alfombra ha sido calificada aún en la edición activa, el sistema debe mostrar "Aún no hay calificaciones registradas para esta edición" en lugar de una lista vacía sin explicación.

---

## GAL-01 — Publicación de fotografía en la galería general

| Identificador de la historia | GAL-01 | Nombre de la historia | Publicación de fotografía en la galería general |
|---|---|---|---|

**RF relacionado (tal cual):**
> RF10. El sistema debe permitir a los usuarios subir fotografías a una galería general, independiente de las alfombras inscritas en el concurso.
> RF11. El sistema debe permitir asociar autor y descripción a cada fotografía de la galería general.

**Historia**
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

---

## ANU-01 — Publicación de un anuncio por el administrador

| Identificador de la historia | ANU-01 | Nombre de la historia | Publicación de un anuncio por el administrador |
|---|---|---|---|

**RF relacionado (propuesto, tal cual se planteó):**
> RF27. El sistema debe permitir al administrador publicar anuncios (texto, y opcionalmente imagen) para informar a los usuarios.

**Historia**
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

---

## ANU-02 — Visualización de anuncios en el frontend

| Identificador de la historia | ANU-02 | Nombre de la historia | Visualización de anuncios en el frontend |
|---|---|---|---|

**RF relacionado (propuesto, tal cual se planteó):**
> RF28. El sistema debe mostrar los anuncios publicados en el frontend para que cualquier visitante los pueda ver.

**Historia**
Como visitante, quiero ver los anuncios publicados por el administrador en el frontend, para estar informado de las novedades de la fiesta.

**Campos visibles del anuncio**
- Título, contenido, imagen (si tiene), fecha de publicación

**Criterio de aceptación**
- El sistema debe mostrar únicamente los anuncios marcados como activos; los anuncios eliminados o desactivados no deben mostrarse bajo ninguna condición.
- Los anuncios deben mostrarse ordenados de la fecha de publicación más reciente a la más antigua.
- Si no existe ningún anuncio activo, el sistema debe mostrar "No hay anuncios disponibles por el momento" en lugar de una sección vacía sin explicación.

---

## ANU-03 — Edición o eliminación de un anuncio

| Identificador de la historia | ANU-03 | Nombre de la historia | Edición o eliminación de un anuncio |
|---|---|---|---|

**RF relacionado (propuesto, tal cual se planteó):**
> RF29. El administrador debe poder editar o eliminar un anuncio publicado.

**Historia**
Como administrador, quiero editar o eliminar un anuncio ya publicado, para corregir información desactualizada o retirar contenido que ya no aplica.

**Acciones disponibles para el administrador**
- Editar
- Eliminar

**Criterio de aceptación**
- Solo un usuario con rol "administrador" puede editar o eliminar un anuncio; si otro rol lo intenta, el sistema debe responder "Acción no autorizada".
- Al editar, el sistema debe actualizar el contenido existente sin generar un nuevo registro ni duplicar el anuncio.
- Al eliminar, el sistema debe ocultar el anuncio del frontend de inmediato; se recomienda una eliminación lógica (marcar como inactivo) en vez de borrarlo físicamente, para conservar el historial administrativo.
- Si el administrador intenta editar o eliminar un anuncio que ya no existe (por ejemplo, eliminado por otro administrador en paralelo), el sistema debe mostrar "El anuncio ya no está disponible".
