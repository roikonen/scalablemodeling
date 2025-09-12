---
date:
  created: 2025-09-12
slug: integrity-cycle
---

# The Integrity Cycle via Three Guardians

Distributed systems cannot escape the limits of distribution:

- Communication across nodes, services or geographies is never instantaneous
- Network latency, partitions and the speed of light guarantee that **eventual consistency** is the default state

Yet systems must still process requests, enforce business rules and maintain integrity. To cope with these realities, I describe a set of recurring patterns that form the foundation of distributed architectures.

I call them the **Three Guardians**, and together they form the **Integrity Cycle**:

- **Gatekeeper**: the front-line validator that filters commands across consistency boundaries before they reach execution
- **Command Handler**: the authoritative validator and executor of state changes inside a single consistency boundary
- **Event Handler**: the asynchronous reactor that ensures system-wide convergence after domain facts are published

The cycle works as follows:

1. A command is checked by a Gatekeeper across consistency boundaries
2. If accepted, it is passed to a Command Handler, which validates it synchronously within its own consistency boundary and either executes it or rejects it
3. The Command Handler produces events as facts when state changes occur
4. Event Handlers consume these events and drive reactions across the system

## Example: Ordering a product while it is being removed

Consider a system with customers, orders and products:

- A customer issues a command to place an order for a product
- The **Gatekeeper** checks the product model to verify the product exists and forwards the command
- The **Command Handler** loads the order model, validates the customer account and records the order if the model’s invariants hold
- The **Event Handler** listens for the resulting *OrderPlaced* event and updates models or workflows

Now imagine that at the same moment, a product removal command is also being processed:

- The Gatekeeper for product removal checks the order model to see if there are pending orders, but due to distribution it may see stale data
- This allows the order and the removal to pass validation at the same time
- The Command Handlers execute both commands inside their models and emit events
- The Event Handlers then detect the conflict: an order was placed for a product that has been removed
- The Event Handlers reconcile by canceling the order and compensating the customer

This example illustrates the Integrity Cycle:

- The **Gatekeeper** prevents many invalid commands
- The **Command Handler** enforces invariants within its model
- The **Event Handler** reconciles the remaining conflicts asynchronously

The cycle balances **prevention, enforcement and reconciliation** to maintain integrity in a world where global synchrony is impossible.

---

## Command Handler

### Context
Every distributed system must change state in response to incoming requests. These requests, modeled as **commands**, represent intent to perform an action (for example *PlaceOrder* or *RemoveProduct*).

Within a **single consistency boundary** such as a model or a transactional resource, state changes can be validated and applied synchronously. Distribution does not eliminate the need for authoritative state, it just means that such validation is localized to where the model lives.

### Problem
How can we process commands safely against a mutable model, ensuring correctness in the presence of concurrent modifications?

### Forces

- **Concurrency**: Multiple commands may target the same state simultaneously
- **Integrity**: Domain invariants must hold (for example inventory cannot go negative)
- **Performance**: Users expect responsive command execution
- **Isolation**: We want to validate against authoritative state without requiring distributed consensus

### Solution
A **Command Handler** is responsible for executing a command against an authoritative model. It validates the command synchronously against that model and applies state changes if valid.

Concurrency is handled using **optimistic concurrency control** (checking versions and rejecting on conflict) or **pessimistic locking** (blocking until safe).

### Implementation

- Map each command to a corresponding Command Handler
- Load the relevant model
- Validate command preconditions against current state
- Apply the state change and persist it
- Return success or failure

### Example

- *PlaceOrder*: Load the customer model, check available balance, record the order if sufficient funds exist
- *ShipOrder*: Load the order model, check that it has been paid, mark as shipped

### Resulting Context
- Each command is validated against authoritative state
- Concurrent modifications are either serialized or rejected
- Integrity is preserved inside the consistency boundary of the model

### Consequences
✅ Ensures correctness of state changes  
✅ Provides immediate validation against authoritative data  
⚠️ Does not protect against stale commands originating outside the consistency boundary

### Related Patterns
- **Gatekeeper**: Filters commands across boundaries before they reach the Command Handler
- **Event Handler**: Coordinates asynchronous reactions after command execution

---

## Event Handler

### Context
In distributed systems, actions often produce consequences beyond the immediate consistency boundary. Commands executed in one place generate **events**, domain facts describing what has happened (for example *OrderPlaced* or *ProductRemoved*). These facts must often be communicated to other parts of the system which may run on different nodes, services or geographies.

Because of this **distribution**, events cannot always be reacted to synchronously. Network latency, partitions and independent scaling force asynchronous handling. This distribution naturally results in **eventual consistency** between components.

### Problem
How can we ensure that different parts of the system react appropriately to domain events, even though event delivery is asynchronous and queried state may be stale?

### Forces

- **Asynchrony**: Event delivery has no global ordering guarantee
- **Resilience**: Failures must not result in lost reactions
- **Idempotency**: Events may be delivered more than once
- **Context**: Reacting to an event often requires **additional information** gathered through models
- **Staleness**: Queried state may lag due to eventual consistency

### Solution
Use **Event Handlers** to subscribe to domain events. An Event Handler:

- Consumes **events as facts**
- Optionally **queries additional models** to enrich its decision
- Triggers **side effects** such as updating models, starting workflows, notifying external systems or compensating for conflicts

Like **Gatekeepers**, Event Handlers operate with the best available view of state and must tolerate stale data. The key difference is **when** they act:

- **Gatekeeper**: Validates commands *before execution*
- **Event Handler**: Reacts to events *after execution*

Together, they form a two-stage defense: Gatekeepers prevent many invalid actions upfront while Event Handlers reconcile and extend the system after facts have been established.

### Implementation

- Listen for domain events emitted by authoritative Command Handlers
- Handle events **idempotently** (safe to reprocess)
- Query models to gather supporting context
- Trigger appropriate side effects such as updating models, notifying services, initiating workflows or performing compensations
- Use retries and dead-letter queues for robustness

### Example

- *OrderPlaced* event: The handler queries the **customer model** to decide whether to trigger a loyalty program workflow
- *ProductRemoved* event: The handler queries the **order model** to find all active orders that still reference the product and cancels them
- *Race condition resolution*: If an order was accepted for a product being removed, the Event Handler compensates by canceling the order and notifying the customer

### Resulting Context
- Domain events are propagated as immutable facts
- Other parts of the system respond to those facts asynchronously
- Business rules are enforced over time, using the best available state at the moment of reaction

### Consequences
✅ Ensures system-wide reactions to authoritative facts  
✅ Supports long-running workflows and cross-boundary coordination  
⚠️ Event Handlers may base decisions on stale query results  
⚠️ Complexity of retries, ordering and compensation must be managed carefully

### Related Patterns
- **Command Handler**: Produces events as authoritative facts
- **Gatekeeper**: Prevents many invalid commands upfront, reducing cleanup burden
- **Saga**: A higher-level orchestration built on Event Handlers

---

## Gatekeeper

### Context
Distributed systems inevitably span multiple nodes, regions or even continents. Because of this **distribution**, communication across components is never instantaneous. It is bounded by network latency and the speed of light.

The consequence is **eventual consistency**: state observed in one part of the system may lag behind state in another. When commands are received, they often require validation that involves **fetching data across these consistency boundaries**. This means the Gatekeeper may consult **stale or incomplete models** during validation.

### Problem
How can we validate and execute commands safely in an environment where the data required for validation may not be fully up-to-date?

### Forces

- **Physics**: Network latency and the speed of light make instantaneous global coordination impossible
- **Eventual consistency**: Both commands and validation queries may see stale or partial data
- **Integrity**: Business rules must still hold (for example orders cannot reference missing products)
- **Responsiveness**: Over-synchronization increases latency and hurts availability
- **Practicality**: Global transactions are too costly and fragile at scale

### Solution
Introduce a **Gatekeeper** that intercepts commands *before dispatch*. The Gatekeeper validates preconditions by consulting models. It acknowledges that data may be stale, so it enforces logical rules based on the **best available view**.

If checks fail, the command is rejected. If checks succeed, the command is passed on to the **Command Handler** for execution. For cases where distribution causes timing conflicts, combine the Gatekeeper with **Event Handlers** that reconcile state over time.

### Implementation

- Place the Gatekeeper between incoming **Command** and the **Command Handler**
- Use models to validate logical preconditions
- Keep Gatekeepers lightweight and avoid introducing side effects
- Reject or forward commands based on validation outcome
- Pair with **Event Handlers** for long-term consistency guarantees

### Example

- A customer places an order: the Gatekeeper verifies that the referenced product exists in the product model
- A product is about to be removed: the Gatekeeper checks the order model to see if open orders still reference it
- **Race condition**: A customer places an order for a product at the exact same moment that the product is being removed
    - The Gatekeeper may validate against stale state and approve the order
    - This race condition cannot be fully avoided due to distribution and asynchronous propagation
    - Such conflicts are caught later by **Event Handlers**, which reconcile the system state (for example canceling the invalid order or compensating the customer)

### Resulting Context
- The system remains available and fast because global synchronization is avoided
- Logical safety nets prevent most invalid commands from corrupting state
- Remaining inconsistencies are delegated to asynchronous reconciliation

### Consequences
✅ Prevents many invalid operations at command boundaries  
✅ Keeps write-side execution simple by moving validation out  
⚠️ Dependent on model freshness, stale data can cause false negatives or positives

### Related Patterns
- **Event Handler**: Ensures eventual reconciliation of state inconsistencies
- **Command Handler**: Executes commands only after Gatekeeper approval  
 
