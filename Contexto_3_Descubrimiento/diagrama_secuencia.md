sequenceDiagram
    actor Usuario
    participant API_Descubrimiento as API Descubrimiento (Contexto 3)
    participant Engine as Engine Recomendaciones
    participant DB_Descubrimiento as BD Personalización
    participant API_Catalogo as API Catálogo (Contexto 2)

    Usuario->>API_Descubrimiento: GET /feeds/home (Bearer JWT)
    activate API_Descubrimiento
    API_Descubrimiento->>DB_Descubrimiento: Obtener UserInterestProfile del usuario
    DB_Descubrimiento-->>API_Descubrimiento: Perfil (Categorías e historial)
    
    API_Descubrimiento->>Engine: generateHomeFeed(profileId)
    activate Engine
    Engine->>Engine: Seleccionar candidatos y aplicar algoritmo de ranking
    Engine-->>API_Descubrimiento: Lista de IDs ordenados
    deactivate Engine

    %% Consulta síncrona justa y necesaria para validar visibilidad antes de mostrar
    API_Descubrimiento->>API_Catalogo: Consultar visibilidad de los IDs (País, edad)
    API_Catalogo-->>API_Descubrimiento: IDs válidos y visibles

    API_Descubrimiento-->>Usuario: 200 OK (Lista de videos recomendados filtrada)
    deactivate API_Descubrimiento