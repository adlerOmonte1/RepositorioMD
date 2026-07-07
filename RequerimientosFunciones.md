
## Requerimientos Funcionales

**Autenticación**
- RF01. El sistema debe permitir a un usuario registrarse mediante nombre de usuario, correo electrónico y contraseña.
- RF02. El sistema debe permitir a un usuario autenticarse (iniciar sesión) con su correo electrónico y contraseña registrados.
- RF03. El sistema debe permitir a un usuario registrarse e iniciar sesión mediante autenticación de Google.
- RF04. El sistema debe impedir el registro de un correo electrónico que ya se encuentre registrado en el sistema.

**Concurso de Alfombras**
- RF05. El sistema debe permitir a un usuario inscribir una alfombra indicando título, descripción, lugar y nombre del autor, el cual puede ser distinto del usuario que realiza la publicación.
- RF06. El sistema debe permitir adjuntar múltiples fotografías a cada alfombra inscrita pero máximo 3 imágenes.
- RF07. El sistema debe registrar cada alfombra en estado "pendiente" hasta que un administrador la apruebe.
- RF08. El sistema debe impedir que una alfombra no aprobada se muestre en la galería pública.
- RF09. El administrador debe poder aprobar o rechazar cada alfombra inscrita.
- RF10. El sistema debe mostrar en una galería pública todas las alfombras aprobadas junto con sus fotografías.
- RF11. El sistema debe permitir a los usuarios calificar y dar "me gusta" a las alfombras publicadas.
- RF12. El sistema debe impedir que un mismo usuario califique más de una vez la misma alfombra.
- RF13. El sistema debe generar un ranking de alfombras en base a las calificaciones y "me gusta" recibidos, para que los espectadores vean cuáles son las mejores.

**Galería general**
- RF14. El sistema debe permitir a los usuarios subir fotografías a una galería general, independiente de las alfombras inscritas en el concurso.
- RF15. El sistema debe permitir asociar autor y descripción a cada fotografía de la galería general.

**Dinámica (Kahoot)**
- RF16. El sistema debe contar con un banco de preguntas de opción múltiple (cuatro alternativas), cada una con una respuesta correcta y explicación opcional.
- RF17. El sistema debe permitir a un usuario responder preguntas en modalidad tipo Kahoot, registrando puntaje y tiempo de respuesta.
- RF18. El sistema debe impedir que un usuario responda la misma pregunta más de una vez dentro del juego Kahoot.
- RF19. El sistema debe generar un ranking de resultados del juego Kahoot.

**Ediciones**
- RF20. El sistema debe organizar toda la información como alfombras, calificaciones y resultados de juegos según la edición a la que pertenece.
- RF21. El administrador debe poder crear una nueva edición al iniciar la fiesta de cada año.
- RF22. El sistema debe permitir cerrar una edición al finalizar el periodo correspondiente.
- RF23. El sistema debe permitir consultar el historial de ediciones anteriores.
- RF24. El sistema debe mostrar públicamente el listado de ganadores de cada edición, para conservar el registro histórico de la fiesta con el paso de los años.

**Anuncios**
- RF25. El sistema debe permitir al administrador publicar anuncios (texto, y opcionalmente imagen) para informar a los usuarios.
- RF26. El sistema debe mostrar los anuncios publicados en el frontend para que cualquier visitante los pueda ver.
- RF27. El administrador debe poder editar o eliminar un anuncio publicado.

---
