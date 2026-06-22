classDiagram
    class RecommendationEngine {
        <<Domain Service>>
        +generateHomeFeed(profileId: String) List~DiscoveryItem~
        +rankCandidates(candidates: List~DiscoveryItem~) List~DiscoveryItem~
    }

    class UserInterestProfile {
        <<Aggregate Root>>
        +String profileId
        +List~SearchSignal~ searchHistory
        +List~String~ preferredCategories
        +recordInteraction(contentId: String, score: float)
    }

    class DiscoveryItem {
        <<Entity>>
        +String itemId
        +String itemType "VIDEO / CHANNEL"
        +List~String~ searchTags
        +float globalPopularityScore
    }

    class SearchSignal {
        <<Value Object>>
        +String queryText
        +DateTime searchedAt
    }

    UserInterestProfile "1" *-- "0..*" SearchSignal : tracks
    RecommendationEngine ..> UserInterestProfile : analyzes
    RecommendationEngine ..> DiscoveryItem : Ranks