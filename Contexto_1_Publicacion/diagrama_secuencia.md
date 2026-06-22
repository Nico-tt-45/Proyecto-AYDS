sequenceDiagram
    actor Creador
    participant API_Publicacion as API Publicación (Contexto 1)
    participant Storage as Almacenamiento (S3/Blob)
    participant Transcoder as Servicio de Transcodificación
    participant EventBus as Message Broker (Eventos)
    participant API_Catalogo as API Catálogo (Contexto 2)

    Creador->>API_Publicacion: POST /uploads (fileName, size)
    API_Publicacion-->>Creador: 201 OK (assetId, uploadUrl)
    
    Creador->>Storage: PUT Archivo Binario en uploadUrl
    Storage-->>API_Publicacion: Evento: Archivo recibido
    
    API_Publicacion->>API_Publicacion: Cambiar estado a PROCESSING
    API_Publicacion->>Transcoder: POST /assets/{assetId}/process
    activate Transcoder
    Transcoder-->>API_Publicacion: Transcodificación finalizada (1080p, 720p)
    deactivate Transcoder
    
    API_Publicacion->>API_Publicacion: Cambiar estado a READY
    
    %% Integración mediante Eventos
    API_Publicacion->>EventBus: Emitir "AssetReadyForPublication" (assetId, creatorId)
    EventBus-->>API_Catalogo: Consumir evento
    API_Catalogo->>API_Catalogo: Habilitar asset para vinculación editorial
