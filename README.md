# Scalable Modeling – Turning Growth from Challenge to Advantage

This repository offers a **pragmatic toolbox** for designing **scalable systems**. Every new system should be built with 
scalability in mind—**scalability is essential for success**. Growth should be an opportunity for enhancement, though it 
comes with complexities that need to be strategically managed. With **Scalable Modeling**, you can embed scalability in 
your system design from the start. Influenced by Clean Architecture, Domain-Driven Design, EventStorming, and CQRS, this 
approach remains flexible and doesn't rigidly adhere to any single methodology.


## Table of Content

<!-- TOC -->
* [Scalable Modeling – Turning Growth from Challenge to Advantage](#scalable-modeling--turning-growth-from-challenge-to-advantage)
  * [Table of Content](#table-of-content)
  * [High Level Overview](#high-level-overview)
    * [Opportunities](#opportunities)
    * [The Method](#the-method)
      * [Shift Left](#shift-left)
      * [Start From Events](#start-from-events)
    * [Challenges](#challenges)
  * [Why to Concentrate Domain Knowledge & Scalability](#why-to-concentrate-domain-knowledge--scalability)
    * [Domain Knowledge is the Most Underrated Key to High Development Velocity and Quality](#domain-knowledge-is-the-most-underrated-key-to-high-development-velocity-and-quality)
    * [Scalability is Prerequisite of Success](#scalability-is-prerequisite-of-success)
* [Scalable Modeling – The Upfront Modeling Technique](#scalable-modeling--the-upfront-modeling-technique)
  * [Theory](#theory)
    * [Event Centrism](#event-centrism)
    * [Event-Driven Architecture (EDA)](#event-driven-architecture-eda)
    * [Command Query Responsibility Segregation (CQRS)](#command-query-responsibility-segregation-cqrs)
    * [Vertical & Horizontal Scalability](#vertical--horizontal-scalability)
    * [Event Sourcing](#event-sourcing)
    * [The Three Dimensions to Scalability](#the-three-dimensions-to-scalability)
      * [Scale Cube](#scale-cube)
      * [Decomposition](#decomposition)
      * [Duplication](#duplication)
      * [Partition](#partition)
  * [Components](#components)
    * [Events](#events)
    * [Ubiquitous Language](#ubiquitous-language)
    * [Commands & State](#commands--state)
    * [Queries](#queries)
    * [Policies](#policies)
    * [Hotspots & Descriptions](#hotspots--descriptions)
    * [Consistency Boundaries](#consistency-boundaries)
  * [Challenges](#challenges-1)
    * [Deduplication](#deduplication)
    * [Tailoring Consistency](#tailoring-consistency)
    * [Time Travel](#time-travel)
  * [End Results](#end-results)
  * [Credits](#credits)
* [License For Using the Pictures](#license-for-using-the-pictures)
<!-- TOC -->

## High Level Overview

There are **three opportunities** and **three challenges** in scalability and understanding them takes you far already.
Combining the opportunities & challenges with an **upfront modeling technique** provides a solid foundations for 
modeling scalable systems.

### Opportunities

1. **[Decomposition](#decomposition)** - scale by splitting different things
2. **[Duplication](#duplication)** - scale by cloning
3. **[Partition](#partition)** - scale by splitting similar things

**Immutability** plays key role in each aspect. 

### The Method

Software is ultimately a **model** — a conceptual solution that, while invisible, solves real-world challenges. 
In software engineering, three aspects are critical:

1. **Understanding**: **WHY** the software is needed (understanding the **purpose** and the **problem** it should address).
2. **Designing**: **WHAT** is the **conceptual model** for the solution.
3. **Developing**: **HOW** the solution is **implemented**.

![](pictures/0_why_what_how.png)

Scalable Modeling serves as an opinionated bridge from **WHY** to **HOW**, with a primary focus on the **WHAT**.

Iteration is naturally much cheaper when it is done on the conceptual model rather than on the implementation level.

#### Shift Left

> "There is nothing so useless as doing efficiently that which should not be done at all."  
> _**Peter Drucker**_

![](pictures/0_shift_left.png)
Scalable Modeling is a method for shifting left in the software engineering process. It helps crystallize the **'why'** 
by focusing on the **'what,'** allowing the creation of a result that serves as an opinionated bridge to the **'how.'**

#### Start From Events

Domain knowledge should be the starting point in building the **conceptual model**:

> "It's developer (mis)understanding that's released in production, not the experts' knowledge."  
> _**Alberto Brandolini**_

Without a proper understanding of the domain, it's impossible to implement a conceptual model that accurately reflects
it. Effective collaboration with domain experts is essential to bridge this gap. **Events** play a central role in
uncovering domain insights and fostering a shared language. The **immutability of events** contributes significantly
to scalability, particularly in event-driven architectures.
**Thus, we adopt an [event-centric](#event-centrism) approach**.

![0_scalable_modeling_components.png](pictures/0_scalable_modeling_components.png)
_Justification for the red arrow in [section: Queries](#queries)._

More about the method in chapter: [The Upfront Modeling Technique](#scalable-modeling--the-upfront-modeling-technique).

### Challenges

1. **[Deduplication](#deduplication)** - as exactly-once delivery is impossible in distributed systems
2. **[Tailoring Consistency](#tailoring-consistency)** - as strong consistency is the wrong default
3. **[Time Travel](#time-travel)** - as distribution causes eventual consistency

## Why to Concentrate Domain Knowledge & Scalability

### Domain Knowledge is the Most Underrated Key to High Development Velocity and Quality

![1_domain_knowledge_is_key.excalidraw.png](pictures/1_domain_knowledge_is_key.excalidraw.png)

> "A complex system that works is invariably found to have evolved from a simple system that worked."
> _**John Gall**_

Design flaws in the simple system tend to compound and lead to exponentially increasing complexity in the complex system.

> "It's developer (mis)understanding that's released in production, not the experts' knowledge."
> _**Alberto Brandolini**_

Without a proper understanding of the domain, it's impossible to implement a conceptual model that accurately reflects it. Effective collaboration with domain experts is essential to bridge this gap.

> "Doing the wrong thing right is not nearly as good as doing the right thing wrong."
> _**Russel L. Ackoff**_

Without a proper understanding of the domain, it's easy to prioritize technical correctness over solving the actual problem.

> "Organizations which design systems are constrained to produce designs which are copies of the communication structures of these organizations."
> _**Melvin E. Conway**_

Conceptual models derived from the domain often lead to software structures that should mirror the communication patterns within the organization.

> "Civilization advances by extending the number of important operations which we can perform without thinking of them."
> _**Alfred North Whitehead**_

When we can focus solely on implementing conceptual models from the domain without worrying about the technical details we can abstract away, we achieve the highest velocity.

### Scalability is Prerequisite of Success

![2_need_for_scalability.excalidraw.png](pictures/2_need_for_scalability.excalidraw.png)

Building a scalable foundation allows businesses to adapt to increasing demands and capitalize on opportunities without being limited by technical constraints. As these influential leaders have noted, scalability is not an afterthought; it is integral to achieving sustainable success.

Without a scalable system from day one, even the best ideas can be slowed down by bottlenecks and inefficiencies as they grow. Designing with scalability in mind ensures that as demand increases, systems can grow exponentially to meet those demands without sacrificing performance or creating technical debt.

> "The faster you scale, the more wealth you create."
> _**Reid Hoffman** (Co-founder of LinkedIn)_

> "In order to win, you must be able to scale exponentially."
> _**Marc Andreessen** (Founder of Andreessen Horowitz)_

> "The most scalable businesses in the world are software businesses."
> _**Bill Gates** (Founder of Microsoft)_

Success is not just about growing fast—it's about building the right infrastructure from the outset, so that growth becomes an advantage, not a challenge.

# Scalable Modeling – The Upfront Modeling Technique

## Theory

### Event Centrism

In **Event Centrism**, everything we experience is either an event or a trace of one. Reality is not made up of *static
objects* but a continuous flow of occurrences, where everything, from a falling leaf to a mountain, is part of an
ongoing process. Even seemingly **permanent things** are *temporary outcomes* of past events, always subject to change.

Our lives are **driven by events** — every thought, action, and emotion is triggered by something, and memories are a
collection of past events that define *who we are*. Traces of past events — like an old building or a weathered book —
remind us of what once occurred, continuing to shape the present.

Events can also be *potential*, waiting to happen, or *hidden*, unfolding beyond our perception, like biological
processes or distant cosmic phenomena. Every event is part of a **cause-and-effect continuum**, influencing future
occurrences. **Time**, in this view, is meaningful only as the medium through which events unfold.

Even the **self** is an ongoing event, constantly shaped by experiences and interactions. In *Event Centrism*,
everything in life is *fluid, dynamic, and interconnected*, emphasizing that our world and our identities are in
constant motion, driven by the events we experience.

**Event Centrism** is not an *absolute truth* but rather one way to understand the world — a very useful way to *model
systems*.

### Event-Driven Architecture (EDA)

Event-Driven Architecture (EDA) is a design pattern where systems react to events in near real-time. Components
communicate by producing, detecting, and responding to events, enabling asynchronous processing and loose coupling
between services, which allows for more scalable and resilient systems.

### Command Query Responsibility Segregation (CQRS)

CQRS is a pattern that separates the responsibilities of updating data (commands) and reading data (queries). By
dividing these operations, CQRS improves performance, scalability, and security, allowing for more efficient handling
of complex, high-demand systems.

Scalable Modeling does not go into purism in CQRS - in Scalable Modeling queries can (when well justified) also query command models for
improved consistency where it does not jeopardise the scalability. Commands can also return simple data like sequence number of the produced events.

### Vertical & Horizontal Scalability

![3_vertical_horizontal_scalability.excalidraw.png](pictures/3_vertical_horizontal_scalability.excalidraw.png)
[Universal Scalability Law, Wikipedia](https://en.wikipedia.org/wiki/Neil_J._Gunther#Universal_Scalability_Law)

Limiting contention is the key for higher scalability.

### Event Sourcing

![4_event_sourcing.excalidraw.png](pictures/4_event_sourcing.excalidraw.png)

### The Three Dimensions to Scalability

In this section we are in the context of:
* Event-Driven Architecture (EDA)
* Command Query Separation (CQS)
* Command Query Responsibility Segregation (CQRS)

#### Scale Cube

![5_0_scale_cube.png](pictures/5_0_scale_cube.png)

#### Decomposition

![5_1_decomposition.png](pictures/5_1_decomposition.png)

#### Duplication

![5_2_duplication.png](pictures/5_2_duplication.png)

#### Partition

![5_3_partition.png](pictures/5_3_partition.png)

## Components

1. [Events](#events)
2. [Ubiquitous Language](#ubiquitous-language)
3. [Commands & State](#commands--state)
4. [Queries](#queries)
5. [Policies](#policies)
6. [Hotspots & Descriptions](#hotspots--descriptions)
7. [Consistency Boundaries](#consistency-boundaries)

### Events

![6_1_events_1.png](pictures/6_1_events_1.png)

![6_1_events_2.png](pictures/6_1_events_2.png)

### Ubiquitous Language

![](pictures/6_2_ubiquitous_language.png)

### Commands & State

![](pictures/6_3_commands.png)

### Queries

![](pictures/6_4_queries_1.png)

I understand that **querying the command model** (red arrow in the picture above) is a topic that often sparks strong
opinions. In my view, the command model serves as a special type of model that validates commands, and
while it is not designed for querying, there are cases where it can be read from if necessary. For example, in
in-memory command models [Akka-style](https://akka.io/), where the model is clustered, a valid use case might involve
querying the state of an entity immediately after its creation or update. Since the entity is already in memory,
fetching it quickly from there can be justified. However, querying the command model should be avoided wherever
possible, as there are many ways this can lead to misuse, such as introducing performance bottlenecks or
inconsistencies. For this reason, querying the command model requires strong justification.

![](pictures/6_4_queries_2.png)

### Policies

![](pictures/6_5_policies_1.png)

![](pictures/6_5_policies_2.png)

### Hotspots & Descriptions

![](pictures/6_6_hotsposts_descriptions.png)

### Consistency Boundaries

![](pictures/6_7_consistency_boundaries.png)

Interestingly, even if this is done as a last step in the Scalable Modeling, this often requires **business-driven 
decisions**. For instance, while financial transactions demand strict consistency, less critical processes like 
reporting can tolerate delays.

## Challenges

### Deduplication

![7_1_deduplication.png](pictures/7_1_deduplication.png)

### Tailoring Consistency

In distributed systems, **defining consistency boundaries** is crucial for balancing performance and correctness. These
boundaries determine where strong consistency is enforced and where eventual consistency is acceptable. Interestingly,
tailoring these boundaries often requires **business-driven decisions**. For instance, while financial transactions
demand strict consistency, less critical processes like reporting can tolerate delays.

By explicitly defining consistency boundaries, you ensure the system aligns with business needs while optimizing
scalability. This approach prevents over-engineering and allows the system to scale efficiently without unnecessary
constraints.

![7_2_tailoring_consistency.png](pictures/7_2_tailoring_consistency.png)

### Time Travel

![7_3_time_travel.png](pictures/7_3_time_travel.png)

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

## End Results

![6_components_of_scalable_modeling.png](pictures/6_components_of_scalable_modeling.png)

## Credits

The following persons have had a lot of influence on what this repository describes:

* **Alberto Brandolini**: Creator of *Event Storming*, a collaborative workshop technique used to explore and model 
  complex business processes through domain events. His approach helps teams rapidly gain insights into business 
  domains by focusing on key events that drive processes.

* **Eric Evans**: Known for pioneering *Domain-Driven Design (DDD)*, a software development philosophy that emphasizes 
  aligning the software model closely with the business domain. His work focuses on creating a shared understanding 
  between technical teams and domain experts to ensure the software reflects real-world complexity.

* **Gregory Young**: Renowned for developing and promoting *Command Query Responsibility Segregation (CQRS)* and 
  *Event Sourcing*. His work centers on separating read and write operations in systems, improving scalability, 
  and using event sourcing to maintain the history of all changes in a system, offering resilience and insights 
  into past system states.

# License For Using the Pictures

![copyright.png](pictures/copyright.png)

See [License](LICENSE.md).
