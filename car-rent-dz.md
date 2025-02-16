# Car rent Dz 
## Requirements and functionalities 
- [ ] core system functionalities:
    - [ ] Geo-location system integration
    - [ ] User logs as stats
    - [ ] Notfification broadcasting
    - [ ] User docs management
    - [ ] (not in contract) Payment integration 
- [ ] particular user functionalities:
    - [ ] Auth options integration
    - [ ] Listing
         - [ ] Location based Listing
         - [ ] Agency profiles listing 
    - [ ] Promotions
         - [ ] by admin
         - [ ] by agency
    - [ ] Profile controll
    - [ ] Searching
        - [ ] by time range
        - [ ] by distance
        - [ ] price
        - [ ] rating
        - [ ] car-type
        - [ ] (notin contract) transmission type
    - [ ] Reservation management 
- [ ] Agency Owner
- [ ] Admin - pseudo-admin
      
### Car Reservation Status Flow

```mermaid
stateDiagram-v2
    [*] --> INITIATED
    INITIATED --> PAYMENT_CONFIRMED: paid
    PAYMENT_CONFIRMED --> CANCELLED: cancel request
    PAYMENT_CONFIRMED --> IN_PROGRESS: car delivered
    
    IN_PROGRESS --> DELIVERY_ISSUE: issue reported
    DELIVERY_ISSUE --> IN_PROGRESS_EXTENDED: resolved with extension
    DELIVERY_ISSUE --> IN_PROGRESS: resolved without extension
    
    IN_PROGRESS --> IN_PROGRESS_EXTENDED: extension requested
    IN_PROGRESS --> RETURN_ISSUE: issue at return
    IN_PROGRESS_EXTENDED --> RETURN_ISSUE: issue at return
    
    RETURN_ISSUE --> COMPLETED_WITH_PENALTY: resolved with tax
    RETURN_ISSUE --> COMPLETED: resolved without tax
    
    IN_PROGRESS --> COMPLETED: normal return
    IN_PROGRESS_EXTENDED --> COMPLETED: normal return
    
    CANCELLED --> [*]
    COMPLETED --> [*]
    COMPLETED_WITH_PENALTY --> [*]
```

---

### Detailed Reservation Flow

```mermaid
stateDiagram-v2
    [*] --> Initialized
    Initialized --> Paid: User proceeds to payment
    
    state Paid {
        [*] --> AwaitingConfirmation
        AwaitingConfirmation --> Confirmed: Payment confirmed
        Confirmed --> Cancelled: User cancels
        Cancelled --> Refunded: Refund processed
    }
    
    Paid --> Active: Payment confirmed
    
    state Active {
        [*] --> Ongoing
        Ongoing --> DeliveryIssue: Problem during delivery
        state DeliveryIssue {
            [*] --> UnderReview
            UnderReview --> ResolvedWithExtension: Extension granted
            UnderReview --> ResolvedWithoutExtension: Resolved normally
            UnderReview --> Refunded: Issue unresolvable
        }
        ResolvedWithExtension --> Extended
        ResolvedWithoutExtension --> Ongoing
    }
    
    Active --> EndPhase: Reservation period ends
    
    state EndPhase {
        [*] --> Evaluation
        Evaluation --> Extended: User requests extension
        Evaluation --> ReturnIssue: Problem detected
    }
    
    Extended --> Active: Continue with extended period
    
    state ReturnIssue {
        [*] --> Inspection
        Inspection --> ResolvedWithTax: Additional charges
        Inspection --> ResolvedWithoutTax: No charges
    }
    
    ReturnIssue --> ReturnConfirmed: Resolution complete
    EndPhase --> ReturnConfirmed: No issues detected
    Refunded --> [*]
    ReturnConfirmed --> [*]
```
