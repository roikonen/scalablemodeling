---
date:
  created: 2025-09-14
slug: integrity-chain
---

# The Integrity Chain: Guardians of Distributed Systems

Distributed systems cannot escape their fundamental limits:

- Communication across nodes, services or geographies is never instantaneous
- Network latency, partitions and the speed of light guarantee that **eventual consistency** is the default state

Yet systems must still process requests, enforce business rules and maintain integrity. To cope with these realities, I use a set of recurring patterns in **[Scalable Modeling](https://roikonen.github.io/scalablemodeling)** that form the foundation for growth-ready scalable systems.

I call them the **Three Guardians**, and together they form the **Integrity Chain**:

- **[Gatekeeper](https://roikonen.github.io/scalablemodeling/components/#gatekeeper)**: the front-line validator that filters commands **across consistency boundaries** before they reach execution
- **[Command Handler](https://roikonen.github.io/scalablemodeling/components/#command-handler)**: the authoritative validator and executor of state changes **inside a single consistency boundary**
- **[Event Handler](https://roikonen.github.io/scalablemodeling/components/#event-handler)**: the **asynchronous reactor** that makes sure all parts of the system catch up after domain facts are published

The chain works as follows:

1. A command is checked by a Gatekeeper across consistency boundaries
2. If accepted, it is passed to a Command Handler, which validates it synchronously within its own consistency boundary and either executes it or rejects it
3. The Command Handler produces events as facts when state changes occur
4. Event Handlers consume these events and drive reactions across the system

![Integrity Chain Overview](images/20250914-1.png#only-light)  
![Integrity Chain Overview](images/20250914-1-dark.png#only-dark)

!!! note

    In addition to the Three Guardians, Scalable Modeling also describes the **Query Handler** and the **Data Processor**. A Query Handler serves read requests by consulting a single view. A Data Processor, on the other hand, combines data from multiple sources or views to maintain derived models. These roles are important but relatively straightforward, so I have not explored them in detail in this post.

## Example: Ordering and Archiving a Product

![Ordering vs Archiving Conflict](images/20250914-2.png#only-light)  
![Ordering vs Archiving Conflict](images/20250914-2-dark.png#only-dark)

Consider a system where customers can order products, but products can also be archived (removed from sale):

- A customer issues a command to *Order Product X*
- The **Gatekeeper** checks that Product X exists and forwards the command
- The **Command Handler** for the order validates the customer account and records the order if valid
- The **Event Handler** later reacts to the *OrderPlaced* event and updates projections

At the same time, an administrator issues a command to *Archive Product X*:

- The **Gatekeeper** for product archiving checks for open orders, but due to distribution it may see stale state and allow the archive request to pass
- The **Command Handler** archives Product X inside its consistency boundary and emits *ProductArchived*
- The **Event Handler** consumes this event, queries for open orders of Product X and issues compensating actions such as canceling those orders and refunding customers. It waits long enough for the open orders view to catch up so it reflects all orders for Product X after the product was archived.

This demonstrates how the Integrity Chain works:

- **Gatekeeper** prevents many invalid commands but cannot guarantee global freshness
- **Command Handler** enforces invariants locally and emits authoritative events
- **Event Handler** detects cross-boundary conflicts and reconciles asynchronously

## Compensating Actions as First-Class Features

In distributed systems **compensating actions** are not merely technical cleanups. They are **first-class business features** and just as important as the original actions that triggered the need for correction.

It is rarely enough to simply *delete* or *undo* an operation. In complex domains such as logistics or finance values and states are constantly changing. An order may affect billing, a shipment may alter routing, an invoice may trigger tax reports. If a mistake occurs after one of these actions a dedicated correction process must be triggered. These processes must be explicitly modeled as part of the business domain.

Examples include:

- **Billing and payments**: A correction flow issues refunds or credit notes rather than silently rolling back the invoice
- **Logistics**: A compensating flow reroutes trucks, updates manifests or notifies partners
- **Regulation**: A correction flow files a new report rather than erasing the old one

These compensation and correction flows handle the inevitable **gaps between feasibility checks and actual execution** in distributed systems. They ensure not only technical consistency but also **real-world accountability** especially in domains where financial or regulatory stakeholders are involved. Ignoring them leads to messy downstream edits and fragile workarounds.

In the Integrity Chain **Event Handlers** are the natural home for modeling such flows since they react asynchronously to established facts and can drive corrective action across boundaries.

## Tradeoffs in Handling the Archive-Order Race

When a product is archived at the same time as new orders are placed, the system must reconcile the conflict. There are several ways to do this, each with different tradeoffs:

- **Rely on views and compensate later**: Archive first, then let an Event Handler query an eventually consistent view of open orders. Once the view has caught up, compensating actions cancel or refund invalid orders. This keeps product and order flows loosely coupled, but reconciliation is delayed and customers may briefly see invalid orders.

![Delayed compensation through Event Handler](images/20250914-2.png#only-light){ width="800" }  
![Delayed compensation through Event Handler](images/20250914-2-dark.png#only-dark){ width="800" }

- **Compensate immediately**: When a product is archived, there is a short window where new orders may still slip in. If an order attempts to register to a product that has already been archived, the product rejects the registration. When the Event Handler sees this rejection, it issues a compensating action such as canceling the order.

![Immediate compensation during archival](images/20250914-4.png#only-light){ width="600" }  
![Immediate compensation during archival](images/20250914-4-dark.png#only-dark){ width="600" }

No single option is universally correct. The choice depends on the domain:

- Finance may favor immediate compensation to protect against invalid transactions
- E-commerce may accept delayed reconciliation for looser coupling and higher throughput
- Logistics may mix strategies, blocking for some operations while reconciling asynchronously for others

The key is to recognize that compensation, consistency and coupling are all levers. Each domain must strike its own balance.

## Why This Matters

In a globally distributed system, models such as products, customers and orders exist in multiple places. Their read copies may lag behind one another. The Integrity Chain embraces this reality:

![Distributed Models](images/20250914-3.png#only-light){ width="300" }  
![Distributed Models](images/20250914-3-dark.png#only-dark){ width="300" }

- **Prevention** at the edge with Gatekeepers
- **Enforcement** within consistency boundaries via Command Handlers
- **Reconciliation** across the system through Event Handlers

The result is integrity without requiring impossible global synchrony.
