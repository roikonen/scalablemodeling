# What is Scalable Modeling?

## The Problem: When Growth Becomes Your Enemy

Most software systems start small and simple. A single server, a straightforward database, perhaps a monolithic application that handles everything. This works beautifully until it doesn't. As user demand grows, what once was your advantage (rapid development, simple deployment) becomes your constraint. Database queries slow down, servers crash under load, and making changes feels like performing surgery on a patient that can't be anesthetized.

The traditional response? _"We'll scale when we need to."_ But by then, you're redesigning fundamental architecture under pressure, with users waiting and revenue at stake. It's like trying to widen a highway during rush hour.

## The Solution: Design Patterns That Scale Naturally

![](assets/images/scalable_modeling_components.png#only-light)
![](assets/images/scalable_modeling_components_dark.png#only-dark)
/// caption
Justification for the red arrows in sections: [Queries](components/#queries) & [Time Travel](challenges/#time-travel).
///

Scalable Modeling is a design methodology that uses patterns and structures that naturally support growth. You can start with the simplest possible implementation and evolve it incrementally as needs arise.

Think of it like choosing the right building materials. You might start with a small structure, but by using materials and techniques that support expansion, you can grow organically without rebuilding from scratch. The key is making choices that keep your options open.

## Core Approach: Event-Centric Architecture

The Scalable Modeling methodology combines two complementary approaches:

**Scalable Modeling** focuses on understanding and designing systems by drawing from:

- **[Domain-Driven Design](credits/#eric-evans) (DDD)** for business alignment and domain modeling
- **[EventStorming](credits/#alberto-brandolini)** for collaborative modeling and discovery

**[CEQS (Command-Event-Query Separation)](ceqs/#ceqs-command-event-query-separation)** focuses on architectural implementation by drawing from:

- **[Clean Architecture](credits/#robert-c-martin)** for separation of concerns
- **[Event-Driven Architecture](theory/#event-driven-architecture-eda) (EDA)** for loose coupling
- **[CQRS](theory/#command-query-responsibility-segregation-cqrs)** for read/write optimization

Rather than rigidly following any single approach, this methodology synthesizes modeling and architectural concepts into a flexible system that adapts to your specific needs.

## The Foundation: Opportunities and Challenges

Scalable Modeling recognizes that scalability involves both opportunities to leverage and challenges to overcome:

### Three Opportunities for Scale
1. **[Decomposition](opportunities/#decomposition)** - Scale by splitting different things (services, domains, responsibilities)
2. **[Duplication](opportunities/#duplication)** - Scale by cloning (horizontal scaling, redundancy)
3. **[Partition](opportunities/#partition)** - Scale by splitting similar things (data sharding, load distribution)

### Three Challenges to Address
1. **[Deduplication](challenges/#deduplication)** - Managing duplicate messages since exactly-once delivery is impossible in distributed systems
2. **[Tailoring Consistency](challenges/#tailoring-consistency)** - Choosing appropriate consistency models beyond "strong by default"
3. **[Time Travel](challenges/#time-travel)** - Handling eventual consistency across distributed components

The key insight: **immutability of messages and events** plays a crucial role in leveraging each opportunity while mitigating each challenge.

## The Building Blocks

![](assets/images/components_of_scalable_modeling.png#only-light)
![](assets/images/components_of_scalable_modeling_dark.png#only-dark)

Scalable Modeling provides specific components for modeling scalable systems:

- **[Events](components/#events)** - Immutable records that enable distribution and scalability (both private and public)
- **[Commands and State](components/#commands-and-state)** - Validated operations that modify state according to business rules
- **[Queries](components/#queries)** - Read operations typically separated from command models
- **[Policies](components/#policies)** - Rules that glue parts together
- **[Bounded Contexts](components/#bounded-contexts)** - Clear boundaries around domain areas with specific language and rules
- **[Ubiquitous Language](components/#ubiquitous-language)** - Shared vocabulary between all stakeholders
- **[Consistency Boundaries](components/#consistency-boundaries)** - Enable tailoring consistency

These components work together to create systems where each piece has a clear purpose and can evolve independently.

## The Bridge: From Why to How

Software engineering involves three critical phases:

- **Understanding** (WHY): What problem are we solving and for whom?
- **Designing** (WHAT): What is our conceptual model for the solution?
- **Developing** (HOW): How do we implement this solution?

![](assets/images/why_what_how.png#only-light)
![](assets/images/why_what_how_dark.png#only-dark)

Scalable Modeling serves as an opinionated bridge from WHY to HOW, with primary focus on the WHAT. By investing in conceptual modeling before implementation, you iterate faster and cheaper. Changing a diagram is typically orders of magnitude easier than refactoring production code. CEQS provides guardrails for the HOW, ensuring your implementation choices support scalability.

## Your Journey Ahead

This methodology isn't about upfront complexity or building features you don't need yet. It's about choosing patterns and structures that naturally accommodate growth. You can start with a simple implementation and evolve it incrementally as your requirements change.

Whether you're starting a new project or looking to refactor an existing system, Scalable Modeling provides the conceptual tools to make evolution easier. When your system needs to handle more load or complexity, you'll have an architecture that can adapt rather than one that must be rebuilt.

Whether you're starting a new project or looking to refactor an existing system, Scalable Modeling provides the conceptual tools to design for growth. The following sections will guide you through the practical application of these principles, helping you build systems where growth becomes an advantage rather than an obstacle.

## License For Using the Pictures

![](assets/images/copyright.png#only-light){ width="200" }
![](assets/images/copyright_dark.png#only-dark){ width="200" }

See [License](https://github.com/roikonen/scalablemodeling/blob/main/LICENSE.md).