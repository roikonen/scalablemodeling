---
date:
  created: 2025-09-26
slug: models-vs-state
---

# Models vs State: Perspectives in Distributed Systems

When I speak about distributed systems I often say *models* instead of *state*.  
This raises questions: *“Aren’t they the same thing?”*

Not quite. In distributed environments **state is never singular**. What exists instead are multiple evolving perspectives derived from the same events. That is why I prefer to talk about **models**.

## State in Centralized Systems

In a centralized database *state* is simple to grasp:

- There is a single authoritative snapshot
- Reads and writes happen in one place
- Everyone sees the same truth

This mental model breaks down as soon as we step into distributed environments.

![Centralized vs Distributed State](images/20250926-1.png#only-light){ width="600" }  
![Centralized vs Distributed State](images/20250926-1-dark.png#only-dark){ width="600" }

## What is State in Distributed Systems

Even the smallest component has state: a variable in memory, a cache entry, a row in a database.  
A distributed system is the combination of many such local states, spread across nodes and services.

The tricky part is that these states are not always consistent with each other. Delays, partitions and independent updates prevent us from treating the collection as one global truth.

And what we call *state* depends on perspective:

- Sometimes state means a **snapshot** of values at a point in time
- Sometimes state means the **sequence of events** that brought the system to this point

So while state is essential it is also ambiguous.

**State = raw condition of a component or system at some point in time (snapshot or events)**

## Models as Structured Interpretations

This is where models come in.  
A model is not just raw state but a designed projection of it. Models answer questions and support decisions. They may be built from snapshots or from event streams, but always with a purpose in mind.

**Model = structured interpretation of that state for a particular purpose**

![Events feeding multiple models](images/20250926-2.png#only-light){ width="700" }  
![Events feeding multiple models](images/20250926-2-dark.png#only-dark){ width="700" }

## Command Models and Query Models

Not all models are equal.

- A **query model** is optimized for answering questions quickly. It projects events into a read-friendly structure that can be used for reporting, APIs or dashboards.
- A **command model** works together with a **command handler**. It looks very much like a query model but contains **restrictive structures** that enforce rules.

For example, a query model of cats might happily list cats with five legs if such data is present. A command model, on the other hand, would define that a cat can have at most four legs and would reject any command that tries to create or modify a cat outside that rule.

In this sense, command models are not just views but **guardians of invariants** inside their consistency boundaries.

## Why Models not State

Talking about *state* suggests there is one global truth. In reality:

- Each node or service maintains its own local state
- These local states do not automatically combine into one consistent picture
- Different models can be built from the same events each serving a distinct business purpose

By speaking in terms of *models* we emphasize **plurality and flexibility** rather than a single frozen picture.

![Multiple projections from the same log](images/20250926-3.png#only-light){ width="700" }  
![Multiple projections from the same log](images/20250926-3-dark.png#only-dark){ width="700" }

## Example: Orders and Inventory

Consider an ecommerce system:

- The **Order Model** projects events into a view of customer purchases
- The **Inventory Model** projects events into a view of stock levels
- Both are built from the same event log yet they highlight different truths

If a network partition delays updates one model may lag behind the other. This is not an error it is the natural state of distributed systems.

![Orders vs Inventory projections](images/20250926-4.png#only-light){ width="600" }  
![Orders vs Inventory projections](images/20250926-4-dark.png#only-dark){ width="600" }

## Why This Matters

By shifting the language from *state* to *models*:

- We stop pretending there is one global snapshot
- We accept eventual consistency as normal
- We design systems where multiple perspectives coexist

This mindset encourages architectures that are more scalable resilient and adaptable to real world complexity.

!!! note

    In **[Scalable Modeling](https://roikonen.github.io/scalablemodeling)** models are treated as first-class citizens: each projection is explicitly designed for its role. This makes it possible to grow systems without forcing everything into a single global “state.”
