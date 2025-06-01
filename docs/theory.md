# Short Prerequisite Theory

## Event-Driven Architecture (EDA)

Event-Driven Architecture (EDA) is a design pattern where systems react to events in near real-time. Components
communicate by producing, detecting, and responding to events, enabling asynchronous processing and loose coupling
between services, which allows for more scalable and resilient systems.

![](assets/images/eda.png#only-light)
![](assets/images/eda_dark.png#only-dark)

## Command–Query Separation (CQS)

Command–Query Separation (CQS) is a design principle introduced by Bertrand Meyer during his work on the Eiffel programming language. It defines a strict division between operations that mutate system state (commands) and operations that retrieve information (queries). Each method should be either a command or a query but not both. This separation improves program clarity by preventing hidden side-effects in queries and it supports testing and reasoning by preserving functional purity in information retrieval.

## Command Query Responsibility Segregation (CQRS)

CQRS is a pattern built on top of CQS that separates the responsibilities of updating data (commands) and reading data (queries). By dividing these operations, CQRS improves performance, scalability and security, enabling more efficient handling of complex or high-demand systems.

![](assets/images/cqrs.png#only-light)
![](assets/images/cqrs_dark.png#only-dark)

[Scalable Modeling](https://roikonen.github.io/scalablemodeling/) does not go into purism in CQRS - 
in [Scalable Modeling](https://roikonen.github.io/scalablemodeling/) queries can (when well justified) also query 
command models for improved consistency where it does not jeopardize the scalability. Command model may later fork to 
query model(s), and commands can also return simple data like sequence number of the produced events.

## Vertical & Horizontal Scalability

![](assets/images/vertical_horizontal_scalability.png#only-light)
![](assets/images/vertical_horizontal_scalability_dark.png#only-dark)
[Universal Scalability Law, Wikipedia](https://en.wikipedia.org/wiki/Neil_J._Gunther#Universal_Scalability_Law)

Limiting contention is the key for higher scalability.

## Event Sourcing

![](assets/images/event_sourcing.png#only-light)
![](assets/images/event_sourcing_dark.png#only-dark)