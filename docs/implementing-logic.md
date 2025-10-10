# Implementing Logic

Now that we’ve learned about the various "sticky notes" that help model the system, the next question is: where should 
we actually implement the business logic? Let’s start by framing the problem. At a high level, systems react to 
**triggers** and produce **effects**. A system has a **state** that follows the rules and structure defined by its 
**model**. Scalable systems consist of multiple states and models, as in distributed environments, there cannot be a 
single state.

!!! info

    In this section I decided to use a notation that is a mix of functional programming and mathematical notation, as I wanted to keep the content of this document technology- and programming-language-agnostic.

Here, function *f* represents the logic that connects the *Trigger* and *State* to the *Effect*.

$$
f(\text{Trigger}, \text{State}) \to \text{Effect}
$$

...but not all logic is business/domain logic, so we need to drill deeper.

## Triggers

$$
\text{Trigger} \in
\begin{cases}
\text{Command} \\
\text{Event} \\
\text{Query}
\end{cases}
$$

Triggers (messages) in the system can be classified as:

* **Commands**: Requests to change the system state.
* **Events**: Facts that describe changes in the system state.
* **Queries**: Requests to retrieve the system state.

## State and Model

A system has a **state** that follows the rules and structure defined by its **model**. Scalable systems consist of 
multiple states and models, as in distributed environments, there cannot be a single state.

Sometimes, the current state of the (sub)system is required by the function that is implementing logic (e.g. for command 
validation), and sometimes it is not (e.g. during event processing).

$$
\text{State} \in \{\emptyset, \text{state}\}
$$

* The **model** defines the structure of the **state**.
* The **state** can only evolve along the pathways permitted by the **model**.

## Effects

$$
\text{Effect} \in
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
- **Query invocation**: Executing one or more queries on the system.
- **State update**: Modifying the current system state.
- **Reply**: Sending a response to the trigger source.

Effects can lead to **chain reactions**, meaning one effect may produce additional effects:

$$
\text{Effect} \to \{\text{Effect}\}^*
$$

## Business/Domain Logic

Returning to the business logic: it can be implemented by using three types of policies: 
**Command Handlers**, **Event Handlers** & **Gatekeepers** - all functions. Other parts of the system primarily deal 
with wiring, integration or restructuring data.

Ideally, business logic is implemented within the **command model** which consist of command handler and state or state projection, but due to the need for distribution
and managing cognitive load, not all business logic can remain in the command models alone.

![](assets/images/business_logic.png#only-light)
![](assets/images/business_logic_dark.png#only-dark)

| **Policy**           | ![](assets/images/policies_command_handler.png#only-light){ width="100" }![](assets/images/policies_command_handler_dark.png#only-dark){ width="100" }                   | ![](assets/images/policies_event_handler.png#only-light){ width="100" }![](assets/images/policies_event_handler_dark.png#only-dark){ width="100" }                                        | ![](assets/images/policies_gatekeeper.png#only-light){ width="100" }![](assets/images/policies_gatekeeper_dark.png#only-dark){ width="100" }     |
|----------------------|---------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|---------------------------------------------------------|
| **Trigger**          | ![](assets/images/commands_command.png#only-light){ width="100" }![](assets/images/commands_command_dark.png#only-dark){ width="100" }                                   | ![](assets/images/events_private.png#only-light){ width="100" }![](assets/images/events_private_dark.png#only-dark){ width="100" }![](assets/images/events_public.png#only-light){ width="100" }![](assets/images/events_public_dark.png#only-dark){ width="100" } | ![](assets/images/commands_command.png#only-light){ width="100" }![](assets/images/commands_command_dark.png#only-dark){ width="100" }           |
| **Responsibility**   | Validation and state mutation                                                   | Choreography between subsystems                                                                  | Validation over consistency boundaries                  |
| **State Dependency** | Direct access to current state                                                  | Access to eventually consistent state                                                            | Access to eventually consistent state                   |
| **Timing**           | Synchronous                                                                     | Asynchronous                                                                                     | Synchronous                                             |
| **Examples**         | *"Remove **product** from **order** if not the last **product** in **order**."* | *"Send a notification after **order** approval."* *"Update stock after **order** approval."*     | *"Add **product** to **order** if **product** exists."* |

### Command Handler

This is the core of the system where the "magic" happens. Commands are validated and applied against the stat or state projection, 
causing the state to evolve.

$$
f(\text{Command}, \text{State}) \to \text{Effect}
$$

$$
\text{Effect} \in
\begin{cases}
\text{Reply} \\
\text{EventEmission} \to \text{Reply} \\
\text{StateUpdate} \to \text{Reply}
\end{cases}
$$

**Function returns:**

* Only **Reply** in case of invalid command.
* **EventEmission** of one to many events in case command is approved and event sourcing is used.
* **StateUpdate** in case command is approved and state storage is used.

### Event Handler

To ensure future scalability and manage the sustainable cognitive load of implementation teams, large models need to be 
[decomposed](../opportunities/#decomposition) into smaller ones. Interactions between these models are defined by **event handlers**, 
which also contain business/domain logic. Event handlers typically implement simple "if-this-then-that" rules.

$$
f(\text{Event}) \to \text{Effect}
$$

$$
\text{Effect} \in
\begin{cases}
\text{CommandDispatch} \\
\text{EventEmission} \\
\text{QueryInvocation} \to
\begin{cases}
\text{CommandDispatch} \\
\text{EventEmission}
\end{cases}
\end{cases}
$$

* **Commands** can be dispatched to cause new effects in other places of the service / bounded context.
* Public **Events** can be emitted/published for other services / bounded contexts to consume.
* **Queries** can be used to enrich the event data before command dispatch or public event emission.

### Gatekeepers

When making large-scale decisions, it is often necessary to consult different parts of the system across consistency 
boundaries before executing a command. The term *Gatekeeper* reflects its intended role, as eventual consistency 
introduces risks in its application. A *Gatekeeper* should be used strictly to verify logical conditions.

$$
f(\text{Command}) \to \text{QueryInvocation} \to \text{CommandDispatch}
$$

For example, the *Gatekeeper* ensures the product has been introduced before it can be added to an order. However, if 
a product can be removed, there must be a process in place to handle open orders that include the removed product.

To ensure data integrity, **Event Handlers** should be used alongside **Gatekeepers**, forming a cohesive process that 
prevents gaps in the eventually consistent system.