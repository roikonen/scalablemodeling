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