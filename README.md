erDiagram
    USUARIO {
        int id PK
        string email UK
        string username UK
        string rol
    }
    EDICION {
        int id PK
        string nombre UK
        int anio
        string estado
    }
    ALFOMBRA {
        int id PK
        int usuario_id FK
        int edicion_id FK
        string titulo
        string estado
    }
    FOTO_ALFOMBRA {
        int id PK
        int alfombra_id FK
        string imagen
        int orden
    }
    CALIFICACION {
        int id PK
        int usuario_id FK
        int alfombra_id FK
        float puntaje
        boolean me_gusta
    }
    GANADOR_ALFOMBRA {
        int id PK
        int alfombra_id FK
        int edicion_id FK
        int puesto
    }
    FOTO_GALERIA {
        int id PK
        int usuario_id FK
        string url
        string autor
        string estado
    }
    ANUNCIO {
        int id PK
        int usuario_id FK
        string titulo
        boolean estado
    }
    PREGUNTA {
        int id PK
        string enunciado
        string correcta
    }
    INTENTO_TRIVIA {
        int id PK
        int usuario_id FK
        int pregunta_id FK
        int edicion_id FK
        int puntaje
        int tiempo_ms
    }

    USUARIO ||--o{ ALFOMBRA : registra
    USUARIO ||--o{ CALIFICACION : califica
    USUARIO ||--o{ FOTO_GALERIA : publica
    USUARIO ||--o{ ANUNCIO : publica
    USUARIO ||--o{ INTENTO_TRIVIA : responde
    EDICION ||--o{ ALFOMBRA : agrupa
    EDICION ||--o{ GANADOR_ALFOMBRA : corresponde
    EDICION ||--o{ INTENTO_TRIVIA : agrupa
    ALFOMBRA ||--o{ FOTO_ALFOMBRA : tiene
    ALFOMBRA ||--o{ CALIFICACION : recibe
    ALFOMBRA ||--o{ GANADOR_ALFOMBRA : premia
    PREGUNTA ||--o{ INTENTO_TRIVIA : tiene
