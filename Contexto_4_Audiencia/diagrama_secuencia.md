sequenceDiagram
    actor Usuario
    participant API_Audiencia as API Audiencia (Contexto 4)
    participant DB_Audiencia as Base de Datos (Audiencia)
    participant EventBus as Message Broker (Kafka/RabbitMQ)
    participant API_Descubrimiento as API Descubrimiento (Contexto 3)

    Usuario->>API_Audiencia: PUT /contents/{id}/reactions (LIKE)
    activate API_Audiencia
    API_Audiencia->>DB_Audiencia: Validar si ya existe reacción
    DB_Audiencia-->>API_Audiencia: OK (Ninguna previa)
    API_Audiencia->>DB_Audiencia: Persistir nueva reacción (LIKE)
    
    %% Integración asíncrona
    API_Audiencia->>EventBus: Publicar evento "EngagementRegistered" (contentId, LIKE)
    
    API_Audiencia-->>Usuario: 200 OK (Reacción registrada)
    deactivate API_Audiencia

    %% Consumo por otro contexto
    EventBus-->>API_Descubrimiento: Consumir evento "EngagementRegistered"
    activate API_Descubrimiento
    API_Descubrimiento->>API_Descubrimiento: Actualizar señales de ranking para el usuario
    deactivate API_Descubrimiento
