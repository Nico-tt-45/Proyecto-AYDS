classDiagram
    class MonetizationProfile {
        <<Aggregate Root>>
        +String creatorId
        +EligibilityStatus status
        +List~EarningEvent~ earningHistory
        +List~Payout~ payoutHistory
        +Money currentBalance
        +addEarning(event: EarningEvent)
        +requestPayout(amount: Money)
    }

    class EarningEvent {
        <<Entity>>
        +String eventId
        +String sourceId "Ej: ID del video o de la membresía"
        +EarningSource source "AD_REVENUE, MEMBERSHIP, TIP"
        +Money amount
        +DateTime recordedAt
    }

    class Payout {
        <<Entity>>
        +String payoutId
        +Money amount
        +PayoutStatus status
        +DateTime requestedAt
    }

    class Money {
        <<Value Object>>
        +BigDecimal amount
        +String currency
    }

    MonetizationProfile "1" *-- "0..*" EarningEvent : records
    MonetizationProfile "1" *-- "0..*" Payout : manages
    MonetizationProfile ..> Money : uses
    EarningEvent ..> Money : uses
    Payout ..> Money : uses