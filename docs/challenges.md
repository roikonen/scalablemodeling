# Challenges

When modeling scalable systems, you need to understand the following three challenges. They relate to distribution and scalability. Even if your implementation is not yet distributed and maintains strong consistency, it is important to anticipate these challenges in advance. Consider how the system could evolve to address them when the need arises.

## Deduplication

![](assets/images/deduplication.png#only-light)
![](assets/images/deduplication_dark.png#only-dark)

Since exactly-once delivery is impossible in distributed systems, we use effectively-once or idempotent processing to 
ensure duplicate messages don’t affect the outcome.

## Tailoring Consistency

!!! quote

    "Developers simply do not implement large scalable applications assuming distributed transactions...
    Two-phase commit is the anti-availability protocol."

    **_Pat Helland_**

In distributed systems, **defining consistency boundaries** is crucial for balancing performance and correctness. These
boundaries determine where strong consistency is enforced and where eventual consistency is acceptable. Interestingly,
tailoring these boundaries often requires **business-driven decisions**. For instance, while financial transactions
demand strict consistency, less critical processes like reporting can tolerate delays.

By explicitly defining consistency boundaries, you ensure the system aligns with business needs while optimizing
scalability. This approach prevents over-engineering and allows the system to scale efficiently without unnecessary
constraints.

![](assets/images/tailoring_consistency.png#only-light)
![](assets/images/tailoring_consistency_dark.png#only-dark)

In the context of scalability, distribution and consistency, it is also worth mentioning **conflict-free replicated data 
types (CRDTs)**. CRDTs enable concurrent updates by multiple nodes without requiring centralized conflict resolution. 
This reduces the need for managing atomic operations in distributed systems. By leveraging CRDTs, consistency can be 
tailored to prioritize availability and eventual alignment, making them an effective choice for scenarios where 
strict consistency boundaries are not critical.

## Time Travel

![](assets/images/time_travel.png#only-light)
![](assets/images/time_travel_dark.png#only-dark)

When using **CQRS** with eventually consistent read models, the system's read models may reflect different points in 
time due to the delay in propagating updates. This allows for a form of "time travel," where users can observe data at 
various stages of consistency. As new events are processed, the read model gradually "catches up" with the latest state. 
However, during this period, the system might expose outdated or future-looking states, effectively letting users or 
systems "travel" between these states.

This is particularly significant when querying or presenting data that spans multiple sources or services across 
consistency boundaries. For example, one source might reflect an older version of the data, while another presents the 
most recent state.

As a result, combining data from these sources may yield conflicting or incoherent views of the overall system. This 
inconsistency poses a challenge when trying to form a unified or accurate perspective of the current state. To mitigate 
these issues, designers need to carefully consider the timing and aggregation of data to avoid misleading or 
inconsistent results, especially in cases where decisions are being made based on these read models.

!!! tip "Avoiding Loops to Prevent Time Travel"

    One common source of time travel issues in distributed systems is the presence of loops in the architecture. Feedback loops between services can introduce subtle timing problems, cause race conditions and lead to eventual consistency violations. These loops make it difficult to reason about the state of the system at any given point and often result in unexpected behavior.

    Loops can cause components to observe partial or out-of-date information or even see future state due to out-of-order event delivery. This breaks the mental model of cause and effect and undermines the reliability of event-driven workflows.

    In general, it's best to design systems using simple acyclic data flows. Unidirectional flows are easier to understand, test and scale. If a feedback mechanism is absolutely necessary, it should be isolated, tightly controlled and made idempotent to avoid cascading inconsistencies.

    **Example: Cross-Entity Uniqueness**

    A typical example of a loop arises when implementing cross-entity uniqueness constraints. For instance, a user might have a unique user ID but also an email address that must be globally unique. Enforcing email uniqueness can easily introduce a loop:

    - The email registry needs to know which user currently owns the address
    - The user entity may need confirmation that the email is available
    - Race conditions occur if two users try to claim the same address at the same time

    To handle this safely, you need a clear protocol to reserve, update and release the email address. This should be done through a dedicated coordination service or component that serializes access to the shared constraint, avoiding circular dependencies between user entities.
    
    Avoiding loops is one of the simplest and most effective ways to reduce complexity and improve consistency in scalable systems.