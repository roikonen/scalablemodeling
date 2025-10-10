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
consistently across services without conflicts. **Immutable streams of private events allow new state projections to be 
projected at any point in a system's lifespan.** In CEQS, model separation (command vs. query) is not strictly enforced 
from day one. When increased complexity, scalability, or usability demands arise, new state projections can be introduced as 
needed. This flexibility means that, unlike in CQRS, in CEQS **model separation is not always necessary upfront**.

Commands, Events and Queries each serve as distinct interfaces to a service:

* **Commands** initiate state changes, resulting in the creation of Events.
* **Events** represent the immutable outcomes of those state changes, facilitating asynchronous communication and 
  providing a historical log.
* **Queries** retrieve current or historical data without causing side effects.

This clear separation of concerns ensures that systems remain scalable, decoupled, and maintainable.

CEQS also further separate events into two categories:

- **Private events**  
  Internal to a service and persisted for internal logic and consistency.

- **Public events**  
  Exposed to other services to represent meaningful business changes and enable communication.

This structure helps manage boundaries between internal behavior and external integration.

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

## Summary

1. **Events as First-Class Citizens**  
   Events are treated as equal partners alongside commands and queries.  
   They are core domain concepts, not just integration artifacts.
 
2. **Explicit Separation of Concerns**  
   CEQS introduces **events** as a third architectural concern in addition to commands and queries, providing clearer boundaries and more flexible composition.
 
3. **Temporal-First Design**  
   Focuses on modeling sequences of events before considering current state or structure.  
   Builds on the temporal thinking from Scalable Modeling.
 
4. **Flexible Consistency Boundaries**  
   Different parts of the system can adopt different consistency models, guided by domain needs instead of technical constraints.

## Questions & Answers

### 1. **What makes [CEQS](../ceqs/) different from traditional CQRS?**

Unlike in CQRS:

- **Events are first-class citizens**  
  Events are treated as core domain concepts, not just side effects or integration tools. They are on equal footing with commands and queries.

- **Gradual query model evolution**  
  The query model is structurally the similar state projection as the command model but without command handlers (i.e. business validation). This allows teams to start with a unified model and separate it only when needed.  
  For example, you might start with an `User` projection used in both commands and queries. Later, if your query load grows or you need specialized views (e.g. user lists), you can evolve the projection independently, by projecting events to form a separate projection.

- **More focus on evolvability**  
  Events help systems adapt and grow over time without requiring major rewrites or early rigid structures.

---

### 2. **What role do Hotspots, Descriptions and Policies play in [CEQS](../ceqs/)?**

These are supporting concepts and tools used alongside CEQS but are not part of its core architecture:

- **Hotspots, descriptions and ubiquitous language**  
  These come from EventStorming and support domain modeling and understanding. They help design better systems but are not unique to CEQS.

- **Policies**  
  Implemented using subscribers and publishers.  
  For example, a subscriber might listen to *"warehouse item low"* and trigger the command *"order more"*.  
  This is a policy expressed as a simple reactive connection between an event and a command.
  Not all subscribers and publishers need complex logic though.