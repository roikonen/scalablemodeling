# Components

Scalable Modeling uses the following components to model scalable systems.

- [Components](#components)
  - [Bounded Contexts](#bounded-contexts)
  - [Events](#events)
  - [Ubiquitous Language](#ubiquitous-language)
  - [Commands and State](#commands-and-state)
  - [Queries](#queries)
  - [Policies](#policies)
  - [Hotspots and Descriptions](#hotspots-and-descriptions)
  - [Consistency Boundaries](#consistency-boundaries)

## Bounded Contexts

Bounded context can be thought as one service in a larger system. It defines a clear boundary around a specific part 
of the domain where particular terms, rules, and models are valid. It helps avoid ambiguity by isolating concepts and 
ensuring they only apply within that boundary. Think of it as a focused "space" where a specific 
[language](../components/#ubiquitous-language) and logic make sense, independent of others.

## Events

![](assets/images/events_1.png#only-light)
![](assets/images/events_1_dark.png#only-dark)

Events are immutable, enabling [duplication](../opportunities/#duplication) which helps in distribution and scalability.
- **Private Events** are specific to the [bounded context](#bounded-contexts).
- **Public Events** are shared externally.

![](assets/images/events_2.png#only-light)
![](assets/images/events_2_dark.png#only-dark)

## Ubiquitous Language

![](assets/images/ubiquitous_language.png#only-light)
![](assets/images/ubiquitous_language_dark.png#only-dark)

Ubiquitous language is more than connections and stickies in the picture above. At best, it is a shared conceptual 
framework that allows both technical and non-technical stakeholders to collaborate effectively, ensuring that all 
parties have a common understanding of the domain and its logic. It helps avoid ambiguity, enabling precise 
communication and design decisions, fostering a deep connection between the code and the business model it represents.

This language allows everyone involved to speak the same language, preventing misunderstandings and ensuring that the 
system's design remains consistent with the business goals and domain requirements.

## Commands and State

![](assets/images/commands.png#only-light)
![](assets/images/commands_dark.png#only-dark)

Commands are validated against a model that frames the state.
The model defines the logic, rules and structure of the state, which is persisted through events or direct updates.
Commands modify the state by adhering to the rules defined by the model, ensuring business invariants are maintained.

## Queries

![](assets/images/queries.png#only-light)
![](assets/images/queries_dark.png#only-dark)

Querying the **Command Model** (illustrated by the red arrow in the picture above) is a topic that often generates 
strong opinions. In essence, the command model is a specialized type of model designed to validate commands. While it 
is not intended for querying, there are certain scenarios where reading from it may be justified.

For example, in **in-memory command models** (such as [Akka-style](https://en.wikipedia.org/wiki/Akka_(toolkit))), where the model is clustered, 
a valid use case might involve querying the state of an entity immediately after its creation or update. Since the 
entity resides in memory, fetching its state quickly can be reasonable and efficient.

Additionally, **immutable streams of private events** allow for the projection of new query models at any point in a 
system's lifecycle. This enables model separation to be implemented on-demand, as needed.

That said, querying the command model can easily lead to misuse, such as performance bottlenecks or data 
inconsistencies. Therefore, as a general rule, it's best to maintain model separation and use the command model for 
queries only when there is a clear, well-justified benefit.

## Policies

![](assets/images/policies_1.png#only-light)
![](assets/images/policies_1_dark.png#only-dark)

![](assets/images/policies_2.png#only-light)
![](assets/images/policies_2_dark.png#only-dark)

![](assets/images/policies_3.png#only-light)
![](assets/images/policies_3_dark.png#only-dark)

![](assets/images/policies_timeline.png#only-light)
![](assets/images/policies_timeline_dark.png#only-dark)

### Command Handler

![](assets/images/policies_command_handler_timeline.png#only-light)
![](assets/images/policies_command_handler_timeline_dark.png#only-dark)

**Context**

Every distributed system must change state in response to incoming requests. These requests, modeled as **commands**, represent intent to perform an action (for example *PlaceOrder*).

Within a **single consistency boundary**, state changes can be validated and applied synchronously. Distribution does not eliminate the need for authoritative state, it just means that such validation is localized to where the model lives.

**Problem**

How can we process commands safely against a mutable model, ensuring correctness in the presence of concurrent modifications?

**Forces**

| Force    | Description                          |
|----------| ------------------------------------ |
| **Concurrency**    | Multiple commands may target the same state simultaneously  |
| **Integrity**    | Domain invariants must hold (for example inventory cannot go negative) |
| **Performance** | Users expect responsive command execution |
| **Isolation** | We want to validate against authoritative state without requiring distributed consensus |

**Solution**

A **Command Handler** is responsible for executing a command against an authoritative model. It validates the command synchronously against that model and applies state changes if valid.

Concurrency is handled using **optimistic concurrency control** (checking versions and rejecting on conflict) or **pessimistic locking** (blocking until safe).

**Implementation**

- Map each command to a corresponding Command Handler
- Load the relevant model
- Validate command preconditions against current state
- Apply the state change and persist it
- Return success or failure

**Example**

- *PlaceOrder*: Load the customer model, check available balance, record the order if sufficient funds exist

**Resulting Context**

- Each command is validated against authoritative state
- Concurrent modifications are either serialized or rejected
- Integrity is preserved inside the consistency boundary of the model

**Consequences**

| Type            | Description                          |
|-----------------| ------------------------------------ |
| ✅ | Ensures correctness of state changes  |
| ✅   | Provides immediate validation against authoritative data |
| ⚠️ | Does not protect against stale commands originating outside the consistency boundary |

**Related Patterns**

- **Gatekeeper**: Filters commands across boundaries before they reach the Command Handler
- **Event Handler**: Coordinates asynchronous reactions after command execution

### Event Handler

![](assets/images/policies_event_handler_timeline.png#only-light)
![](assets/images/policies_event_handler_timeline_dark.png#only-dark)

**Context**

In distributed systems, actions often produce consequences beyond the immediate consistency boundary. Commands executed in one place generate **events**, domain facts describing what has happened (for example *OrderPlaced* or *ProductArchived*). These facts must often be communicated to other parts of the system which may run on different nodes, services or geographies.

Because of this **distribution**, events cannot always be reacted to synchronously. Network latency, partitions and independent scaling force asynchronous handling. This distribution naturally results in **eventual consistency** between components.

**Problem**

How can we ensure that different parts of the system react appropriately to domain events, even though event delivery is asynchronous and queried state may be stale?

**Forces**

| Force           | Description                                                                            |
| --------------- | -------------------------------------------------------------------------------------- |
| **Asynchrony**  | Event delivery has no global ordering guarantee                                        |
| **Resilience**  | Failures must not result in lost reactions                                             |
| **Idempotency** | Events may be delivered more than once                                                 |
| **Context**     | Reacting to an event often requires **additional information** gathered through models |
| **Staleness**   | Queried state may lag due to eventual consistency                                      |

**Solution**

Use **Event Handlers** to subscribe to domain events. An Event Handler:

- Consumes **events as facts**
- Optionally **queries additional models** to enrich its decision
- Triggers **side effects** such as updating models, starting workflows, notifying external systems or compensating for conflicts

Like **Gatekeepers**, Event Handlers operate with the best available view of state and must tolerate stale data. The key difference is **when** they act:

- **Gatekeeper**: Validates commands *before execution*
- **Event Handler**: Reacts to events *after execution*

Together, they form a two-stage defense: Gatekeepers prevent many invalid actions upfront while Event Handlers reconcile and extend the system after facts have been established.

**Implementation**

- Listen for domain events emitted by authoritative Command Handlers
- Handle events **idempotently** (safe to reprocess)
- Query models to gather supporting context
- Trigger appropriate side effects such as updating models, notifying services, initiating workflows or performing compensations
- Use retries and dead-letter queues for robustness

**Example**

- *OrderPlaced* event: The handler queries the **customer model** to decide whether to trigger a loyalty program workflow
- *ProductArchived* event: The handler queries the **order model** to find all active orders that still reference the product and cancels them
- *Race condition resolution*: If an order was accepted for a product being removed, the Event Handler compensates by canceling the order and notifying the customer

**Resulting Context**

- Domain events are propagated as immutable facts
- Other parts of the system respond to those facts asynchronously
- Business rules are enforced over time, using the best available state at the moment of reaction

**Consequences**

| Type | Description                                                                |
| ---- | -------------------------------------------------------------------------- |
| ✅    | Ensures system-wide reactions to authoritative facts                       |
| ✅    | Supports long-running workflows and cross-boundary coordination            |
| ⚠️   | Event Handlers may base decisions on stale query results                   |
| ⚠️   | Complexity of retries, ordering and compensation must be managed carefully |

**Related Patterns**

- **Command Handler**: Produces events as authoritative facts
- **Gatekeeper**: Prevents many invalid commands upfront, reducing cleanup burden
- **Saga**: A higher-level orchestration built on Event Handlers

### Gatekeeper

![](assets/images/policies_gatekeeper_timeline.png#only-light)
![](assets/images/policies_gatekeeper_timeline_dark.png#only-dark)

**Context**

Distributed systems inevitably span multiple nodes, regions or even continents. Because of this **distribution**, communication across components is never instantaneous. It is bounded by network latency and the speed of light.

The consequence is **eventual consistency**: state observed in one part of the system may lag behind state in another. When commands are received, they often require validation that involves **fetching data across these consistency boundaries**. This means the Gatekeeper may consult **stale or incomplete models** during validation.

**Problem**

How can we validate and execute commands safely in an environment where the data required for validation may not be fully up-to-date?

**Forces**

| Force                    | Description                                                                              |
| ------------------------ | ---------------------------------------------------------------------------------------- |
| **Physics**              | Network latency and the speed of light make instantaneous global coordination impossible |
| **Eventual consistency** | Both commands and validation queries may see stale or partial data                       |
| **Integrity**            | Business rules must still hold (for example orders cannot reference missing products)    |
| **Responsiveness**       | Over-synchronization increases latency and hurts availability                            |
| **Practicality**         | Global transactions are too costly and fragile at scale                                  |

**Solution**

Introduce a **Gatekeeper** that intercepts commands *before dispatch*. The Gatekeeper validates preconditions by consulting models. It acknowledges that data may be stale, so it enforces logical rules based on the **best available view**.

If checks fail, the command is rejected. If checks succeed, the command is passed on to the **Command Handler** for execution. For cases where distribution causes timing conflicts, combine the Gatekeeper with **Event Handlers** that reconcile state over time.

**Implementation**

- Place the Gatekeeper between incoming **Command** and the **Command Handler**
- Use models to validate logical preconditions
- Keep Gatekeepers lightweight and avoid introducing side effects
- Reject or forward commands based on validation outcome
- Pair with **Event Handlers** for long-term consistency guarantees

**Example**

- A customer places an order: the Gatekeeper verifies that the referenced product exists in the product model
- A product is about to be archived: the Gatekeeper checks the order model to see if open orders still reference it
- **Race condition**: A customer places an order for a product at the exact same moment that the product is being archived
    - The Gatekeeper may validate against stale state and approve the order
    - This race condition cannot be fully avoided due to distribution and asynchronous propagation
    - Such conflicts are caught later by **Event Handlers**, which reconcile the system state (for example canceling the invalid order or compensating the customer)

**Resulting Context**

- The system remains available and fast because global synchronization is avoided
- Logical safety nets prevent most invalid commands from corrupting state
- Remaining inconsistencies are delegated to asynchronous reconciliation

**Consequences**

| Type | Description                                                                     |
| ---- | ------------------------------------------------------------------------------- |
| ✅    | Prevents many invalid operations at command boundaries                          |
| ✅    | Keeps write-side execution simple by moving validation out                      |
| ⚠️   | Dependent on model freshness, stale data can cause false negatives or positives |

**Related Patterns**

- **Event Handler**: Ensures eventual reconciliation of state inconsistencies
- **Command Handler**: Executes commands only after Gatekeeper approval

## Hotspots and Descriptions

![](assets/images/hotspots_descriptions.png#only-light)
![](assets/images/hotspots_descriptions_dark.png#only-dark)

## Consistency Boundaries

![](assets/images/consistency_boundaries.png#only-light)
![](assets/images/consistency_boundaries_dark.png#only-dark)

Interestingly, even if this is done as a last step in the 
[Scalable Modeling](https://roikonen.github.io/scalablemodeling/), this often requires **business-driven 
decisions**. For instance, while financial transactions demand strict consistency, less critical processes like 
reporting can tolerate delays.

[Dynamic Consistency Boundaries (DCB)](https://dcb.events/) is an emerging term, and as we leave defining the 
consistency boundaries as a last step we make Scalable Modeling compatible with DCBs.