# Diagramas de Secuencia — Ediciones

Los diagramas muestran la interacción desde la perspectiva de uso: **Actor → Sistema → Base de Datos**, sin detallar la lógica interna de desarrollo (capas, clases o servicios del backend).

## 1. Crear nueva edición (EDI-02)

```mermaid
sequenceDiagram
    actor Administrador
    participant Sistema
    participant BaseDeDatos as Base de Datos

    Administrador->>Sistema: Ingresa nombre, año, fecha de inicio y fecha de fin
    Sistema->>BaseDeDatos: Verifica si existe una edición activa
    BaseDeDatos-->>Sistema: Resultado de la verificación

    alt Ya existe una edición activa
        Sistema-->>Administrador: Muestra "Debe cerrar la edición actual antes de abrir una nueva"
    else No existe edición activa
        Sistema->>Sistema: Valida que fecha_fin sea posterior a fecha_inicio
        alt Fechas inválidas
            Sistema-->>Administrador: Muestra "La fecha de fin debe ser posterior a la fecha de inicio"
        else Fechas válidas
            Sistema->>BaseDeDatos: Registra la nueva edición con estado "Activa"
            BaseDeDatos-->>Sistema: Confirma el registro
            Sistema-->>Administrador: Muestra "Edición creada correctamente"
        end
    end
```

## 2. Cerrar la edición en curso (EDI-03)

```mermaid
sequenceDiagram
    actor Administrador
    participant Sistema
    participant BaseDeDatos as Base de Datos

    Administrador->>Sistema: Selecciona "Cerrar edición"
    Sistema->>BaseDeDatos: Consulta el estado actual de la edición
    BaseDeDatos-->>Sistema: Estado ("Activa" o "Cerrada")

    alt La edición ya está cerrada
        Sistema-->>Administrador: Muestra "Esta edición ya está cerrada"
    else La edición está activa
        Sistema-->>Administrador: Solicita confirmación ("Esta acción es irreversible")
        Administrador->>Sistema: Confirma el cierre
        Sistema->>BaseDeDatos: Actualiza el estado a "Cerrada"
        BaseDeDatos-->>Sistema: Confirma la actualización
        Sistema-->>Administrador: Muestra "Edición cerrada correctamente"
    end
```

## 3. Consultar historial de ediciones y cuadro de honor (EDI-04 / EDI-05)

```mermaid
sequenceDiagram
    actor Visitante
    participant Sistema
    participant BaseDeDatos as Base de Datos

    Visitante->>Sistema: Abre el selector de años
    Sistema->>BaseDeDatos: Solicita las ediciones en estado "Cerrada"
    BaseDeDatos-->>Sistema: Lista de ediciones cerradas
    Sistema-->>Visitante: Muestra el listado de años disponibles

    Visitante->>Sistema: Selecciona una edición histórica
    Sistema->>BaseDeDatos: Solicita galería y rankings de esa edición
    BaseDeDatos-->>Sistema: Registros de la edición seleccionada

    alt La edición no tiene alfombras registradas
        Sistema-->>Visitante: Muestra "No existen alfombras registradas en este año"
    else La edición tiene registros
        Sistema-->>Visitante: Muestra galería y rankings de la edición
    end

    Visitante->>Sistema: Accede al "Cuadro de Honor"
    Sistema->>BaseDeDatos: Solicita ganadores de todas las ediciones cerradas
    BaseDeDatos-->>Sistema: Lista de ganadores por edición
    Sistema-->>Visitante: Muestra los tres primeros puestos por año (solo lectura)
```
