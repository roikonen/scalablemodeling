# Opportunities

## The Three Dimensions to Scalability

In this section we are in the context of:

* Event-Driven Architecture (EDA)
* Command Query Separation (CQS)
* Command Query Responsibility Segregation (CQRS)

If these are not familiar to you, please refer to [theory](../theory/) section. 

**Scale Cube**

![](assets/images/scale_cube.png#only-light)
![](assets/images/scale_cube_dark.png#only-dark)

### Decomposition

![](assets/images/decomposition.png#only-light)
![](assets/images/decomposition_dark.png#only-dark)

Decomposition means creating smaller, independently deployable services that together form a system. This involves identifying clear business boundaries where each service owns its commands, events and read models.

- **Clear ownership and isolation**: Services and their development teams operate independently, reducing coordination and integration bottlenecks.
- **Parallel development**: Features can be built in parallel without contention over shared code or data.
- **Tailored scaling**: Each service scales based on its own load profile such as a high-write order service versus a low-traffic reporting service.

Decomposition is about slicing systems along domain boundaries to create independently scalable event-driven units.


### Duplication

![](assets/images/duplication.png#only-light)
![](assets/images/duplication_dark.png#only-dark)

Duplication in Scalable Modeling context means maintaining multiple read models that subscribe to events from the write model.

- **Performance**: Queries target optimized read models without affecting writes.
- **Resilience**: Failures in one projection do not impact others.
- **Recoverability**: Lost projections can be rebuilt by replaying events.

Duplication improves responsiveness and throughput by offloading read concerns from the write model.

### Partition

![](assets/images/partition.png#only-light)
![](assets/images/partition_dark.png#only-dark)

Partitioning splits data and processing by a key such as customer ID, region or tenant. Each partition handles its own subset of commands, events and queries.

- **Horizontal scaling**: Load is distributed across partitions.
- **Performance**: Related operations stay local, reducing cross-partition traffic.
- **Resilience**: Failures in one partition do not affect the whole system.
- **Operational flexibility**: Shards can be deployed, upgraded or scaled independently.

Partitioning allows each segment of the system to operate and evolve in isolation while maintaining global consistency through event streams.