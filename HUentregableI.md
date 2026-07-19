# EDI-01 — Organización y segmentación de información por edición anual
| Campo | Descripción |
|--------|-------------|
| **Identificador** | EDI-01 |
| **Épica** | Gestión de Ediciones |
| **Nombre** | Organización y segmentación de información por edición anual |
| **Prioridad** | Alta |
| **Estado** | Pendiente |
---
## Historia de Usuario
**Como** administrador del sistema,
**Quiero** que toda la información generada durante una edición (alfombras inscritas, calificaciones, resultados de juegos y demás registros relacionados) quede asociada únicamente a la edición correspondiente,
**Para** mantener la independencia de los datos entre años y garantizar consultas, estadísticas y reportes correctos para cada edición.
---
## Descripción
Cada edición representa un evento anual independiente. Toda la información registrada durante una edición deberá quedar vinculada a dicha edición para evitar la mezcla de datos históricos.
Esta organización permitirá que los usuarios consulten información específica de una edición sin afectar ni visualizar registros pertenecientes a otras ediciones.
---
# Reglas de Negocio
RN01.
Toda entidad funcional que represente información propia del evento deberá estar asociada obligatoriamente a una edición.
RN02.
Las consultas realizadas sobre alfombras, calificaciones, rankings o juegos deberán devolver únicamente información correspondiente a la edición seleccionada.
RN03.
Las estadísticas, promedios y rankings deberán calcularse exclusivamente utilizando información de la edición activa.
RN04.
No se permitirá utilizar registros pertenecientes a una edición distinta para cálculos, reportes o visualizaciones.
RN05.
La eliminación o modificación de información de una edición no deberá afectar registros pertenecientes a otras ediciones.
---
# Criterios de Aceptación
## Escenario 1: Consulta de información por edición
**Dado** que existen registros correspondientes a varias ediciones,
**Cuando** el usuario consulta la información de una edición determinada,
**Entonces** únicamente se mostrarán registros pertenecientes a esa edición.
---
## Escenario 2: Registro de información
**Dado** que existe una edición activa,
**Cuando** se registra una nueva alfombra, calificación o resultado de juego,
**Entonces** el sistema deberá asociar automáticamente dicho registro a la edición correspondiente.
---
## Escenario 3: Rankings
**Dado** que existen participantes registrados en diferentes ediciones,
**Cuando** el usuario consulta el ranking de una edición,
**Entonces** únicamente se considerarán los resultados pertenecientes a dicha edición.
---
## Escenario 4: Reportes
**Dado** que el administrador genera un reporte,
**Cuando** selecciona una edición,
**Entonces** el reporte deberá contener exclusivamente información correspondiente a esa edición.
---
# Requerimientos Funcionales Relacionados
- **RF20.** El sistema debe organizar toda la información generada durante el evento según la edición a la que pertenece.
- **RF21.** El sistema debe filtrar automáticamente toda consulta utilizando la edición seleccionada.
- **RF22.** El sistema debe garantizar el aislamiento de los datos entre diferentes ediciones.
---
# Consideraciones Técnicas (No forman parte de la HU)
Las siguientes entidades deberán mantener una relación con la entidad **Edición** mediante una llave foránea:
- Alfombras
- Calificaciones
- Intentos de Trivia
- Ganadores
- (Cualquier otra entidad dependiente del evento)