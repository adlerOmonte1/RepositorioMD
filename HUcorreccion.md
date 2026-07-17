# EDI-01 — Organización y segmentación de información por edición anual

| Campo | Descripción |
|--------|-------------|
| **Identificador** | EDI-01 |
| **Épica** | Gestión de Ediciones |
| **Nombre** | Organización y segmentación de información por edición anual |
| **Prioridad** | Alta |
| **Estado** | Pendiente |

## Historia de Usuario

**Como** administrador del sistema, **quiero** que toda la información generada durante una edición quede asociada exclusivamente a dicha edición **para** mantener la integridad de los datos históricos y evitar la mezcla de información entre diferentes años.

## Descripción

Cada edición representa un evento anual independiente. Toda la información generada durante una edición, como las inscripciones de alfombras, calificaciones, resultados de juegos y demás registros relacionados, deberá mantenerse asociada exclusivamente a la edición correspondiente, garantizando que las consultas, estadísticas y reportes reflejen únicamente la información de esa edición.

## Reglas de Negocio

- **RN01.** Toda información generada durante el evento deberá estar asociada a una única edición.
- **RN02.** Ninguna consulta del sistema deberá combinar información perteneciente a distintas ediciones.
- **RN03.** Los cálculos, estadísticas, rankings y reportes deberán generarse utilizando exclusivamente la información de la edición seleccionada.

## Criterios de Aceptación

**CA01.** Dado que existen registros correspondientes a múltiples ediciones, cuando el usuario consulta la información de una edición específica, entonces el sistema mostrará únicamente los registros asociados a la edición seleccionada.

**CA02.** Dado que existe una edición activa, cuando se registra una nueva alfombra, una calificación o un resultado de juego, entonces el sistema deberá asociar automáticamente dicho registro a la edición correspondiente.

**CA03.** Dado que existen registros de diferentes ediciones, cuando el usuario consulta un ranking, una estadística o un reporte, entonces el sistema deberá generar el resultado utilizando únicamente la información de la edición seleccionada.

## Requerimientos Funcionales Relacionados

- **RF20.** El sistema debe organizar toda la información del evento según la edición a la que pertenece.
- **RF21.** El sistema debe filtrar automáticamente las consultas utilizando la edición seleccionada.
- **RF22.** El sistema debe garantizar el aislamiento de la información entre diferentes ediciones.
