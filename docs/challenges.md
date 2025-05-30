# Challenges

When modeling scalable systems, you need to understand the following three challenges. They relate to distribution and scalability. Even if your implementation is not yet distributed and maintains strong consistency, it is important to anticipate these challenges in advance. Consider how the system could evolve to address them when the need arises.

## Deduplication

![](assets/images/deduplication.png#only-light)
![](assets/images/deduplication_dark.png#only-dark)

Since exactly-once delivery is impossible in distributed systems, we use effectively-once or idempotent processing to 
ensure duplicate messages don’t affect the outcome.

## Tailoring Consistency

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