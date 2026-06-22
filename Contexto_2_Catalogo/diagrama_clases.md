classDiagram
    class CatalogItem {
        <<Aggregate Root>>
        +String catalogItemId
        +String technicalAssetId
        +String channelId
        +ItemStatus status
        +updateMetadata(title, description, tags)
        +publish()
        +unpublish()
    }
    
    class EditorialMetadata {
        <<Value Object>>
        +String title
        +String description
        +List~String~ tags
        +String thumbnailUrl
    }
    
    class VisibilityPolicy {
        <<Entity>>
        +int minimumAge
        +List~String~ blockedCountries
        +boolean isMonetizable
    }
    
    class ItemStatus {
        <<Enumeration>>
        DRAFT
        PUBLISHED
        RESTRICTED
    }
    
    CatalogItem "1" *-- "1" EditorialMetadata : contains
    CatalogItem "1" *-- "1" VisibilityPolicy : enforces
    CatalogItem ..> ItemStatus : uses