{% include mathjax.html %}

# Scalable Modeling – An Event-centric Approach

This [repository](https://github.com/roikonen/scalablemodeling) offers a **modeling technique** for designing 
**scalable systems**. Every new system should be designed with scalability in mind — **scalability is essential for 
success**. With **Scalable Modeling**, you can embed 
scalability in your system design from the start. Influenced by [Clean Architecture](#robert-c-martin), 
[Event-Driven Architecture](#event-driven-architecture-eda) (EDA), 
[Domain-Driven Design](#eric-evans) (DDD), [EventStorming](#alberto-brandolini), and 
[CQRS](#command-query-responsibility-segregation-cqrs), this approach remains flexible and doesn't rigidly adhere to 
any single methodology. That is why this **event-centric architectural approach** has different name: 
[CEQS - Command-Event-Query Separation](#ceqs-command-event-query-separation).

![](pictures/0_scalable_modeling_components.png)
_Justification for the red arrows in sections: [Queries](#queries) & [Time Travel](#time-travel)._

**Webinar recording: [Software engineering for the future: Fast, scalable and built to last](https://www.twoday.fi/en/content/webinars/webinar-software-engineering-for-the-future-fast-scalable-and-built-to-last) (November 26, 2024).**

## Table of Content

<!-- TOC -->
* [Scalable Modeling – An Event-centric Approach](#scalable-modeling--an-event-centric-approach)
  * [Table of Content](#table-of-content)
  * [High Level Overview](#high-level-overview)
    * [Three Opportunities](#three-opportunities)
    * [Software Engineering Flow](#software-engineering-flow)
      * [Shift Left](#shift-left)
      * [Start Modeling From Events](#start-modeling-from-events)
    * [Three Challenges](#three-challenges)
  * [Why to Concentrate Domain Knowledge & Scalability](#why-to-concentrate-domain-knowledge--scalability)
    * [Domain Knowledge is the Most Underrated Key to High Development Velocity and Quality](#domain-knowledge-is-the-most-underrated-key-to-high-development-velocity-and-quality)
    * [Scalability is Prerequisite of Success](#scalability-is-prerequisite-of-success)
* [Scalable Modeling](#scalable-modeling)
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
  * [CEQS: Command-Event-Query Separation](#ceqs-command-event-query-separation)
    * [Architectural Benefits](#architectural-benefits)
  * [Components](#components)
    * [Bounded Contexts](#bounded-contexts)
    * [Events](#events)
    * [Ubiquitous Language](#ubiquitous-language)
    * [Commands & State](#commands--state)
    * [Queries](#queries)
    * [Policies](#policies)
    * [Hotspots & Descriptions](#hotspots--descriptions)
    * [Consistency Boundaries](#consistency-boundaries)
    * [Implementing Logic](#implementing-logic)
      * [Triggers](#triggers)
      * [Effects](#effects)
      * [Business/Domain Logic](#businessdomain-logic)
  * [Challenges](#challenges)
    * [Deduplication](#deduplication)
    * [Tailoring Consistency](#tailoring-consistency)
    * [Time Travel](#time-travel)
  * [Summary of Components](#summary-of-components)
  * [Credits](#credits)
    * [Alberto Brandolini](#alberto-brandolini)
    * [Eric Evans](#eric-evans)
    * [Gregory Young](#gregory-young)
    * [Robert C. Martin](#robert-c-martin)
  * [Background](#background)
    * [About Simo Roikonen](#about-simo-roikonen)
    * [The Trigger that Led to This](#the-trigger-that-led-to-this)
* [License For Using the Pictures](#license-for-using-the-pictures)
<!-- TOC -->

## High Level Overview

There are **three opportunities** and **three challenges** in scalability and understanding them takes you far already.
Combining the opportunities & challenges with an **upfront and iterative modeling technique** provides a solid 
foundations for modeling reliable and scalable systems.

### Three Opportunities

1. **[Decomposition](#decomposition)** - scale by splitting different things
2. **[Duplication](#duplication)** - scale by cloning
3. **[Partition](#partition)** - scale by splitting similar things

**Immutability** (of messages/events) plays key role in each aspect. 

### Software Engineering Flow

Software is ultimately a **model** — a conceptual solution that, while invisible, solves real-world challenges. 
In software engineering, three aspects are critical:

1. **Understanding**: **WHY** the software is needed and by who (understanding the **purpose** and the **problem**
   it should address).
2. **Designing**: **WHAT** is the **conceptual model** for the solution.
3. **Developing**: **HOW** the solution is **implemented**.

![](pictures/0_why_what_how.png)

[Scalable Modeling](https://roikonen.github.io/scalablemodeling/) serves as an opinionated bridge from **WHY** to
**HOW**, with a primary focus on the **WHAT**.

Iteration is naturally much cheaper when it is done on the conceptual model rather than on the implementation level.

#### Shift Left

> "There is nothing so useless as doing efficiently that which should not be done at all."  
> _**Peter Drucker**_

![](pictures/0_shift_left.png)
[Scalable Modeling](https://roikonen.github.io/scalablemodeling/) is a method for shifting left in the software 
engineering process. It helps crystallize the **'why'** by focusing on the **'what,'** allowing the creation of a 
result (the model) that serves as an opinionated bridge to the **'how'**.

#### Start Modeling From Events

To design reliable scalable systems, we need to start from **temporal** thinking (the **flow of time** and how 
things evolve) and gradually move into **spatial** thinking (the **arrangement** of things). In essence, we design 
spatial systems to handle temporal matters – **events**.

**Services are anchored in space but act in time** – At any given moment, a service's location is tied to a physical or 
virtual environment (e.g. a server, container or cluster). Over time, services evolve as they process events, make 
decisions and produce outputs.

**Events are anchored in time but act in space** – Events are fixed to the moment they are created, carrying an 
immutable snapshot of information. As they traverse the system, they move between services and may even be replicated.

{% include youtube.html id="eThvtU0S7kE" %}

So, instead of focusing too much on the **space** between services, consider the **flow of events** and how they
evolve over **time**.

The **immutability of events** contributes significantly to scalability, particularly in event-driven architectures.
Additionally, **events** play a central role in uncovering domain insights and fostering a shared language.
**Thus, we adopt an [event-centric](#event-centrism) approach**.

### Three Challenges

1. **[Deduplication](#deduplication)** - as exactly-once delivery is impossible in distributed systems
2. **[Tailoring Consistency](#tailoring-consistency)** - as strong consistency is the wrong default
3. **[Time Travel](#time-travel)** - as distribution causes eventual consistency

## Why to Concentrate Domain Knowledge & Scalability

### Domain Knowledge is the Most Underrated Key to High Development Velocity and Quality

> "It's developer (mis)understanding that's released in production, not the experts' knowledge."  
> _**Alberto Brandolini**_

Without a proper understanding of the domain, it's impossible to implement a **conceptual model** that accurately 
reflects it. Effective collaboration with domain experts is essential to bridge this gap. **Events** play a central 
role in uncovering domain insights and fostering a shared language. Additionally, the **immutability of events** 
contributes significantly to scalability, particularly in event-driven architectures.

![1_domain_knowledge_is_key.png](pictures/1_domain_knowledge_is_key.png)

> "A complex system that works is invariably found to have evolved from a simple system that worked."
> _**John Gall**_

Design flaws in the simple system tend to compound and lead to exponentially increasing complexity in the complex system.

> "Doing the wrong thing right is not nearly as good as doing the right thing wrong."
> _**Russel L. Ackoff**_

Without a proper understanding of the domain, it's easy to prioritize technical correctness over solving the actual 
problem.

> "Organizations which design systems are constrained to produce designs which are copies of the communication structures of these organizations."
> _**Melvin E. Conway**_

Conceptual models derived from the domain often lead to software structures that should mirror the communication 
patterns within the organization.

> "Civilization advances by extending the number of important operations which we can perform without thinking of them."
> _**Alfred North Whitehead**_

When we can focus solely on implementing conceptual models from the domain without worrying about the technical details 
we can abstract away, we achieve the highest velocity.

### Scalability is Prerequisite of Success

![2_need_for_scalability.png](pictures/2_need_for_scalability.png)

Building a scalable foundation allows businesses to adapt to increasing demands and capitalize on opportunities without 
being limited by technical constraints. As these influential leaders have noted, scalability is not an afterthought; it 
is integral to achieve sustainable success.

Without a scalable system from day one, even the best ideas can be slowed down by bottlenecks and inefficiencies as 
they grow. Designing with scalability in mind ensures that as demand increases, systems can grow exponentially to meet 
those demands without sacrificing performance or creating technical debt.

> "The faster you scale, the more wealth you create."
> _**Reid Hoffman** (Co-founder of LinkedIn)_

> "In order to win, you must be able to scale exponentially."
> _**Marc Andreessen** (Founder of Andreessen Horowitz)_

> "The most scalable businesses in the world are software businesses."
> _**Bill Gates** (Founder of Microsoft)_

Success is not just about growing fast — it's about building the right infrastructure from the outset, so that growth 
becomes an advantage, not a challenge.

# Scalable Modeling

![0_scalable_modeling_components.png](pictures/0_scalable_modeling_components.png)

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

![](pictures/0_eda.png)

### Command Query Responsibility Segregation (CQRS)

CQRS is a pattern that separates the responsibilities of updating data (commands) and reading data (queries). By
dividing these operations, CQRS improves performance, scalability, and security, allowing for more efficient handling
of complex, high-demand systems.

![](pictures/0_cqrs.png)

[Scalable Modeling](https://roikonen.github.io/scalablemodeling/) does not go into purism in CQRS - 
in [Scalable Modeling](https://roikonen.github.io/scalablemodeling/) queries can (when well justified) also query 
command models for improved consistency where it does not jeopardise the scalability. Commands can also return simple 
data like sequence number of the produced events.

### Vertical & Horizontal Scalability

![3_vertical_horizontal_scalability.png](pictures/3_vertical_horizontal_scalability.png)
[Universal Scalability Law, Wikipedia](https://en.wikipedia.org/wiki/Neil_J._Gunther#Universal_Scalability_Law)

Limiting contention is the key for higher scalability.

### Event Sourcing

![4_event_sourcing.png](pictures/4_event_sourcing.png)

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

## CEQS: Command-Event-Query Separation

The diagram below illustrates a novel software architecture concept called **CEQS** — **Command-Event-Query Separation**.
It is an enhancement of the traditional **CQS** (Command Query Separation) pattern highlighting the importance of events
in scalable systems.

![](pictures/6_0_ceqs.png)

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

### Architectural Benefits

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
     * **Scalability:** This architecture supports all [three dimensions of scalability](#the-three-dimensions-to-scalability), 
       ensuring low latency even under high demand.

## Components

1. [Bounded Contexts](#bounded-contexts)
2. [Events](#events)
3. [Ubiquitous Language](#ubiquitous-language)
4. [Commands & State](#commands--state)
5. [Queries](#queries)
6. [Policies](#policies)
7. [Hotspots & Descriptions](#hotspots--descriptions)
8. [Consistency Boundaries](#consistency-boundaries)

### Bounded Contexts

Bounded context can be thought as one service in a larger system. It defines a clear boundary around a specific part 
of the domain where particular terms, rules, and models are valid. It helps avoid ambiguity by isolating concepts and 
ensuring they only apply within that boundary. Think of it as a focused "space" where a specific 
[language](#ubiquitous-language) and logic make sense, independent of others. 

### Events

![6_1_events_1.png](pictures/6_1_events_1.png)

Events are immutable, enabling [duplication](#duplication) which helps in distribution and scalability.
- **Private Events** are specific to the [Bounded Context](#bounded-contexts).
- **Public Events** are shared externally.

![6_1_events_2.png](pictures/6_1_events_2.png)

### Ubiquitous Language

![](pictures/6_2_ubiquitous_language.png)

Ubiquitous language is more than connections and stickies in the picture above. At best, it is a shared conceptual 
framework that allows both technical and non-technical stakeholders to collaborate effectively, ensuring that all 
parties have a common understanding of the domain and its logic. It helps avoid ambiguity, enabling precise 
communication and design decisions, fostering a deep connection between the code and the business model it represents.

This language allows everyone involved to speak the same language, preventing misunderstandings and ensuring that the 
system's design remains consistent with the business goals and domain requirements.

### Commands & State

![](pictures/6_3_commands.png)

### Queries

![](pictures/6_4_queries_1.png)

![](pictures/6_4_queries_2.png)

Querying the **Command Model** (illustrated by the red arrow in the picture above) is a topic that often generates 
strong opinions. In essence, the command model is a specialized type of model designed to validate commands. While it 
is not intended for querying, there are certain scenarios where reading from it may be justified.

For example, in **in-memory command models** (such as [Akka-style](https://akka.io/)), where the model is clustered, 
a valid use case might involve querying the state of an entity immediately after its creation or update. Since the 
entity resides in memory, fetching its state quickly can be reasonable and efficient.

Additionally, **immutable streams of private events** allow for the projection of new query models at any point in a 
system's lifecycle. This enables model separation to be implemented on-demand, as needed.

That said, querying the command model can easily lead to misuse, such as performance bottlenecks or data 
inconsistencies. Therefore, as a general rule, it's best to maintain model separation and use the command model for 
queries only when there is a clear, well-justified benefit.

### Policies

![](pictures/6_5_policies_1.png)

![](pictures/6_5_policies_2.png)

### Hotspots & Descriptions

![](pictures/6_6_hotsposts_descriptions.png)

### Consistency Boundaries

![](pictures/6_7_consistency_boundaries.png)

Interestingly, even if this is done as a last step in the 
[Scalable Modeling](https://roikonen.github.io/scalablemodeling/), this often requires **business-driven 
decisions**. For instance, while financial transactions demand strict consistency, less critical processes like 
reporting can tolerate delays.

Dynamic Consistency Boundaries (DCB) is an emerging term and as we leave defining the consistency boundaries as a last
step we make Scalable Modeling compatible with DCBs.

### Implementing Logic

Now that we’ve learned about the various "sticky notes" that help model the system, the next question is: where should 
we actually implement the business logic? Let’s start by framing the problem.

$$
f(\text{Trigger}, \text{State} \in \{\emptyset, \text{State}\}) \to \text{Effect}
$$

At a high level, systems react to **triggers** and produce **effects**. Sometimes, the current state of the system is 
required (e.g. for command validation), and sometimes it is not (e.g. during event processing). Here, **state** 
refers to the command model and/or query model. 

> Function *f* represents the logic that connects the *Trigger* to the *Effect*. Not all ot the logic is business/domain logic though.


---

#### Triggers

$$
Trigger \in
\begin{cases}
\text{Command} \\
\text{Event} \\
\text{Query}
\end{cases}
$$

Triggers (messages) in the system can be classified as:
- **Commands**: Requests to change the system state.
- **Events**: Facts that describe changes in the system state.
- **Queries**: Requests to retrieve the system state.

---

#### Effects

$$
Effect \in
\begin{cases}
\text{CommandDispatch} \\
\text{EventEmission} \\
\text{QueryInvocation} \\ 
\text{StateUpdate} \\
\text{Reply}
\end{cases}
$$

The system can produce the following effects:
- **Command dispatch**: Issuing a command to the system.
- **Event emission**: Publishing one or more events.
- **Query invocation**: Executing a query on the system.
- **State update**: Modifying the current system state.
- **Reply**: Sending a response to the trigger source.

Effects can lead to **chain reactions**, meaning one effect may produce additional effects:

$$
Effect \to \{\text{Effect}\}^*
$$

---

#### Business/Domain Logic

Returning to the business logic: it can be implemented in two main places using two different types of functions. All 
other parts of the system primarily deal with wiring, integration or visualization.

![](pictures/6_8_business_logic.png)

---

**Command Handler**

This is the core of the system where the "magic" happens. Commands are validated and applied against the model, causing 
the model to evolve. The evolution happens either directly (by applying the command) or indirectly (via events).

$$
f(\text{Command}, \text{State}) \to \text{Effect}
$$

$$
f(\text{Command}, \text{State}) \to
\begin{cases}
\text{Reply} & \text{if invalid command} \\
\text{EventEmission} \to \text{Reply} & \text{if event sourcing} \\
\text{StateUpdate} \to \text{Reply} & \text{if state storage}
\end{cases}
$$

---

**Policy**

To meet scalability requirements, the model is often [decomposed](#decomposition) into smaller pieces. Interactions 
between these pieces are defined by **policies**, which also contain business/domain logic. Policies typically 
implement "if-this-then-that" -type of rules.

$$
f(\text{Event}) \to \text{Effect}
$$

$$
f(\text{Event}) \to
\begin{cases}
\text{CommandDispatch} \\
\text{EventEmission} \\
\text{QueryInvocation} \to \{\text{CommandDispatch}, \text{EventEmission}\}
\end{cases}
$$

* **Commands** can be dispatched to cause new effects in other places of the service / bounded context.
* Public **Events** can be emitted/published for other services / bounded contexts to consume.
* **Queries** can be used to enrich the event data before command dispatch or public event emission.

## Challenges

### Deduplication

![7_1_deduplication.png](pictures/7_1_deduplication.png)

Since exactly-once delivery is impossible in distributed systems, we use effectively-once or idempotent processing to 
ensure duplicate messages don’t affect the outcome.

### Tailoring Consistency

In distributed systems, **defining consistency boundaries** is crucial for balancing performance and correctness. These
boundaries determine where strong consistency is enforced and where eventual consistency is acceptable. Interestingly,
tailoring these boundaries often requires **business-driven decisions**. For instance, while financial transactions
demand strict consistency, less critical processes like reporting can tolerate delays.

By explicitly defining consistency boundaries, you ensure the system aligns with business needs while optimizing
scalability. This approach prevents over-engineering and allows the system to scale efficiently without unnecessary
constraints.

![7_2_tailoring_consistency.png](pictures/7_2_tailoring_consistency.png)

In the context of scalability, distribution and consistency, it is also worth mentioning **conflict-free replicated data 
types (CRDTs)**. CRDTs enable concurrent updates by multiple nodes without requiring centralized conflict resolution. 
This reduces the need for managing atomic operations in distributed systems. By leveraging CRDTs, consistency can be 
tailored to prioritize availability and eventual alignment, making them an effective choice for scenarios where 
strict consistency boundaries are not critical.


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

## Summary of Components

![6_components_of_scalable_modeling.png](pictures/6_components_of_scalable_modeling.png)

## Credits

The following persons have had a lot of influence on what this repository describes:

### Alberto Brandolini
Creator of *Event Storming*, a collaborative workshop technique used to explore and model 
complex business processes through domain events. His approach helps teams rapidly gain insights into business 
domains by focusing on key events that drive processes.

### Eric Evans
Known for pioneering *Domain-Driven Design (DDD)*, a software development philosophy that emphasizes 
aligning the software model closely with the business domain. His work focuses on creating a shared understanding 
between technical teams and domain experts to ensure the software reflects real-world complexity.

### Gregory Young
Renowned for developing and promoting *Command Query Responsibility Segregation (CQRS)* and 
*Event Sourcing*. His work centers on separating read and write operations in systems, improving scalability, 
and using event sourcing to maintain the history of all changes in a system, offering resilience and insights 
into past system states.

### Robert C. Martin
Author of *Clean Architecture* and a key figure in the promotion of software craftsmanship. His 
principles focus on building flexible, maintainable, and scalable systems by adhering to the separation of concerns 
and reducing dependencies between different layers of the system. Martin advocates for architecture that allows 
software to evolve over time, ensuring it remains easy to understand, extend, and refactor, even as requirements 
change. His work is centered on creating systems that prioritize independence from frameworks, databases, and UI, 
ensuring longevity and adaptability in software design.

## Background

### [About Simo Roikonen](about_me.md)

### The Trigger that Led to This
The trigger that led to the creation of this page came from a LinkedIn [post](https://www.linkedin.com/posts/abuijze_the-aggregate-is-great-but-its-time-to-activity-7226485148630843392-gJ_w?utm_source=share&utm_medium=member_desktop) by Allard Buijze.  
The post was about aggregates and their necessity. I studied the post and ended up watching Sara Pellegrini's and Milan 
Savic's talk: [The Aggregate is dead. Long live the Aggregate!](https://www.youtube.com/watch?v=IgigmuHHchI).
After overcoming my cognitive dissonance, I needed to try out the new ideas by drawing them with
[Excalidraw](https://excalidraw.com/), which led to new ideas that I have collected in this [repository](https://github.com/roikonen/scalablemodeling).

Thanks for triggering the ideas!

# License For Using the Pictures

![copyright.png](pictures/copyright.png)

See [License](LICENSE.md).

---

<script src="https://giscus.app/client.js"
        data-repo="roikonen/scalablemodeling"
        data-repo-id="R_kgDOM8SonQ"
        data-category="General"
        data-category-id="DIC_kwDOM8Sonc4Cj6Rl"
        data-mapping="pathname"
        data-strict="0"
        data-reactions-enabled="1"
        data-emit-metadata="0"
        data-input-position="bottom"
        data-theme="light"
        data-lang="en"
        crossorigin="anonymous"
        async>
</script>
