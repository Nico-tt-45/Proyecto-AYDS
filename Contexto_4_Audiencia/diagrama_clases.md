classDiagram
    class Viewer {
        <<Aggregate Root>>
        +String viewerId
        +List~Subscription~ subscriptions
        +subscribeTo(channelId: String)
        +unsubscribeFrom(channelId: String)
    }

    class Subscription {
        <<Entity>>
        +String channelId
        +DateTime subscribedAt
    }

    class ContentInteraction {
        <<Aggregate Root>>
        +String contentId
        +int totalLikes
        +int totalDislikes
        +addReaction(viewerId: String, type: ReactionType)
        +addComment(viewerId: String, text: String)
    }

    class Comment {
        <<Entity>>
        +String commentId
        +String authorId
        +String text
        +DateTime createdAt
        +boolean isPinned
    }

    class ReactionType {
        <<Value Object>>
        +String value "LIKE, DISLIKE, NONE"
    }

    Viewer "1" *-- "0..*" Subscription : has
    ContentInteraction "1" *-- "0..*" Comment : contains
    ContentInteraction ..> ReactionType : uses
