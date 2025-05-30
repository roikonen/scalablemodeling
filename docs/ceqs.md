# CEQS 

Command-Event-Query Separation highlights the crucial role events play in building scalable systems.

## Event-centric Approach

The diagram below illustrates a novel software architecture concept called **CEQS** — **Command-Event-Query Separation**.
It is an enhancement of the traditional **CQS** (Command Query Separation) pattern highlighting the importance of events
in scalable systems.

![](assets/images/ceqs.png#only-light)
![](assets/images/ceqs_dark.png#only-dark)

In scalable systems, events should be treated as first-class citizens, as their immutability is fundamental to achieving 
scalability. Their asynchronous nature enables loose coupling between services, while their immutability allows events 
to be queried or streamed as an accurate record of what has occurred within the system.

Immutability ensures that events are append-only, enabling distributed systems to replicate and process data 
consistently across services without conflicts. **Immutable streams of private events allow new query models to be 
projected at any point in a system's lifespan.** In CEQS, model separation (command vs. query) is not strictly enforced 
from day one. When increased complexity, scalability, or usability demands arise, query models can be introduced as 
needed. This flexibility means that, unlike in CQRS, in CEQS **model separation is not always necessary upfront**.

Commands, Events and Queries each serve as distinct interfaces to a service:

* **Commands** initiate state changes, resulting in the creation of Events.
* **Events** represent the immutable outcomes of those state changes, facilitating asynchronous communication and 
  providing a historical log.
* **Queries** retrieve current or historical data without causing side effects.

This clear separation of concerns ensures that systems remain scalable, decoupled, and maintainable.

## Architectural Benefits

1. **Clean Domain Logic**
    * Separating commands and queries results in clearer, more focused domain logic. Commands handle state changes, 
     while queries handle data retrieval, ensuring that business logic remains concise and purpose-driven.
2. **Separation of Concerns**
    * Commands are responsible for writing data (changing state), while queries are responsible for reading data. This 
     distinction enhances maintainability by providing cleaner code and reducing the risk of unintended side effects.
3. **Loose Coupling**
    * **Within Services:** Commands and queries are decoupled through immutable, private events, allowing each to evolve 
     independently without interference.
    * **Between Services:** Public events decouple microservices, enabling them to operate and scale independently, 
     reducing dependencies between service boundaries.
4. **Resiliency**
    * The system is more resilient because failures in one component (e.g. command processing) do not cascade to others 
     (e.g. queries). This design ensures fault isolation and minimizes downtime.
5. **Near-Real-Time Integrations**
    * Events enable near-real-time communication between services, allowing for fast and seamless integration. This 
     improves responsiveness to changes in the system and facilitates use cases like live updates.
6. **Low Latency at Any Scale**
    * By separating read and write responsibilities, the system can optimize availability and scalability. For example:
        * **High Availability:** Read-heavy workloads can be handled by scaling replicas.
        * **Scalability:** This architecture supports all [three dimensions to scalability](../opportunities/#the-three-dimensions-to-scalability), ensuring low latency even under high demand.