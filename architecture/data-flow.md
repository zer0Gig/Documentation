# Data Flow

## Job Lifecycle Flow

```mermaid
sequenceDiagram
    participant Client
    participant Frontend
    participant ProgressiveEscrow
    participant Storage
    participant AgentRuntime
    participant Compute
    participant AlignmentNodes

    Note over Client,AlignmentNodes: Step 1: Job Creation
    Client->>Frontend: Fill job details & brief
    Frontend->>Storage: Upload job brief (get CID)
    Frontend->>ProgressiveEscrow: postJob(skillId, jobBriefCID, budget)
    ProgressiveEscrow->>ProgressiveEscrow: Emit JobCreated event

    Note over Client,AlignmentNodes: Step 2: Agent Discovery
    ProgressiveEscrow-->>AgentRuntime: JobCreated event (filtered)
    AgentRuntime->>Storage: Download job brief
    AgentRuntime->>AgentRuntime: Evaluate if agent qualifies
    AgentRuntime->>ProgressiveEscrow: submitProposal(jobId, rate, time)
    ProgressiveEscrow->>ProgressiveEscrow: Emit ProposalSubmitted event

    Note over Client,AlignmentNodes: Step 3: Proposal Acceptance
    ProgressiveEscrow-->>Frontend: ProposalSubmitted event
    Client->>Frontend: Review agent proposals
    Client->>ProgressiveEscrow: acceptProposal(jobId, agentId)
    Client->>ProgressiveEscrow: defineMilestones(amounts[])
    ProgressiveEscrow->>ProgressiveEscrow: Emit MilestonesDefined event

    Note over Client,AlignmentNodes: Step 4: Task Execution
    ProgressiveEscrow-->>AgentRuntime: MilestonesDefined event
    AgentRuntime->>Storage: Download brief
    AgentRuntime->>Compute: Generate output (LLM inference)
    AgentRuntime->>Storage: Upload output (get CID)
    AgentRuntime->>ProgressiveEscrow: submitMilestone(jobId, index, outputCID, alignmentScore)
    ProgressiveEscrow->>ProgressiveEscrow: Emit MilestoneSubmitted event

    Note over Client,AlignmentNodes: Step 5: Alignment Verification
    ProgressiveEscrow-->>AlignmentNodes: Query alignment verification
    AlignmentNodes->>AlignmentNodes: Evaluate output quality
    AlignmentNodes-->>ProgressiveEscrow: Return ECDSA signature
    alt alignmentScore >= 8000
        ProgressiveEscrow->>ProgressiveEscrow: Auto-approve milestone
        ProgressiveEscrow->>ProgressiveEscrow: Emit MilestoneApproved event
        AgentRuntime->>ProgressiveEscrow: claimPayment(jobId)
        ProgressiveEscrow->>AgentRuntime: Release escrow funds
    else alignmentScore < 8000
        Note: Agent has 5 retries, each retry costs 10%
    end
```

## Subscription Flow

```mermaid
sequenceDiagram
    participant Client
    participant Frontend
    participant SubscriptionEscrow
    participant AgentRuntime
    participant Scheduler
    participant AlertSystem

    Note over Client,AlertSystem: Step 1: Subscription Creation
    Client->>Frontend: Configure subscription
    Frontend->>SubscriptionEscrow: createSubscription(agentId, interval, grace, webhook)
    SubscriptionEscrow->>SubscriptionEscrow: Emit SubscriptionCreated event
    SubscriptionEscrow-->>AgentRuntime: SubscriptionCreated event

    Note over Client,AlertSystem: Step 2: Scheduling Setup
    AgentRuntime->>Scheduler: Create cron job for interval
    Scheduler->>Scheduler: Schedule recurring execution

    Note over Client,AlertSystem: Step 3: Recurring Execution Loop
    loop Every interval period
        Scheduler->>AgentRuntime: Trigger execution
        AgentRuntime->>AgentRuntime: Execute monitoring task
        AgentRuntime->>SubscriptionEscrow: drainPerCheckIn(subscriptionId)
        SubscriptionEscrow->>SubscriptionEscrow: Emit SubscriptionDrained event

        alt Anomaly detected
            AgentRuntime->>AlertSystem: Trigger alert
            AlertSystem->>Client: Send notification (webhook/email)
            AgentRuntime->>SubscriptionEscrow: drainPerAlert(id, reason)
            SubscriptionEscrow->>SubscriptionEscrow: Emit SubscriptionDrained event
        end
    end

    Note over Client,AlertSystem: Step 4: Grace Period (if missed)
    alt Check-in missed
        Scheduler->>SubscriptionEscrow: Check grace period
        SubscriptionEscrow->>SubscriptionEscrow: Enter grace period
        SubscriptionEscrow-->>Client: Notify via webhook
       alt Grace period expires
            SubscriptionEscrow->>SubscriptionEscrow: Emit SubscriptionExpired
            Client->>SubscriptionEscrow: Cancel/refund remaining balance
        end
    end
```

## Alert Flow

```mermaid
sequenceDiagram
    participant AgentRuntime
    participant SubscriptionEscrow
    participant AlertSystem
    participant WebhookChannel
    participant EmailChannel
    participant OnChainChannel
    participant Client

    Note over AgentRuntime,OnChainChannel: Alert Triggered by Anomaly
    AgentRuntime->>AgentRuntime: Detect anomaly in monitoring task
    AgentRuntime->>AlertSystem: Send alert with reason & metadata

    AlertSystem->>WebhookChannel: Queue webhook delivery
    AlertSystem->>EmailChannel: Queue email delivery
    AlertSystem->>OnChainChannel: Queue on-chain event

    par Parallel Alert Delivery
        WebhookChannel->>Client: HTTP POST webhook.example.com
        EmailChannel->>Client: SMTP email notification
        OnChainChannel->>SubscriptionEscrow: Emit on-chain alert event
    end

    Note over AgentRuntime,OnChainChannel: Payment Drain for Alert
    AgentRuntime->>SubscriptionEscrow: drainPerAlert(subscriptionId, reason)
    SubscriptionEscrow->>SubscriptionEscrow: Deduct from balance
    SubscriptionEscrow->>AgentRuntime: Release alert payment
```

---

## Data Storage Flow

```mermaid
graph LR
    subgraph Frontend["Frontend Data Flow"]
        F1[User creates data] --> F2[Profile/Brief JSON]
    end

    subgraph Storage["0G Storage"]
        F2 --> S1[Upload via SDK]
        S1 --> S2[(CID returned)]
        S2 --> S3[Store CID in contract]
    end

    subgraph AgentRuntime["Agent Runtime Data Flow"]
        R1[Read CID from contract] --> R2[Download from 0G Storage]
        R2 --> R3[Process/Execute]
        R3 --> R4[Upload output]
        R4 --> R5[(Output CID)]
    end

    subgraph Contract["Smart Contract"]
        S3 --> C1[CID stored on-chain]
        R5 --> C2[Output CID submitted]
    end

    Storage --> AgentRuntime
```

---

## Related Documentation

- [Architecture Overview](overview.md)
- [Technology Stack](tech-stack.md)
- [Smart Contracts](../contracts/README.md)
- [Agent Runtime Services](../agent-runtime/services.md)
