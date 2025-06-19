# Why Events?

**Events** are central to modeling because they reflect real things that happen in a business domain. They are factual, meaningful and naturally align with how business people think.

## 1. Events Represent Business Truth

Events are **things that have happened** in the domain (e.g. `OrderPlaced`, `PaymentReceived`, `InvoiceGenerated`). They are past-tense, factual and **undeniable truths** from the business perspective.

- ✅ "OrderPlaced" happened  
- ✅ "EmailSent" happened  
- ❌ "ShouldSendEmail" is not a fact, it’s an intent  

Events are also **immutable**: once they occur, they cannot be changed. This immutability makes them a reliable record of what actually happened, supporting **auditing**, **debugging** and **replayable workflows**.

Focusing on what *has happened* aligns everyone with concrete, observable business behavior.

## 2. Events Are Language Everyone Understands

Everyone in an organization understands business events. They talk about them naturally in meetings:

> “A customer placed an order”  
> “The payment failed”  
> “A reminder was sent”

This makes events a **shared language** between tech and non-tech people, critical for closing the communication gap.

## 3. Events Drive System Behavior

In event-driven systems, **events trigger workflows, reactions and decisions**. They’re not just outcomes, they're **inputs for other parts of the system**.

For example:

- Event: `OrderPlaced`  
- Reaction: Send confirmation email, update inventory, start shipping process

## 4. Events Help Map the Real Workflow

By laying out events on a timeline, you can **see the actual flow of a business process**: what happens first, what follows and what causes what.

This helps:

- Discover hidden logic  
- Spot race conditions  
- Understand dependencies  
- Reveal time-based behaviors
- Find knowledge gaps

## 5. Events Are Stable Over Time

Requirements and processes change but **past events remain valid**. Designing systems around events often leads to **more maintainable and resilient** systems because:

- Events don’t change often (e.g. `OrderPlaced` is always meaningful)  
- New behaviors can react to existing events without breaking existing functionality

## 6. Events Help Discover Domain Insights

When people start talking in terms of events, **domain knowledge surfaces more easily**. Participants begin to ask:

- What caused this event?  
- What should happen next?  
- Who or what handled it?  
- Is this event always followed by another?

This leads to better understanding of **cause-effect relationships** and underlying business logic.

## 7. Events Contribute to Scalability

Events decouple producers and consumers, allowing systems to **scale independently and asynchronously**. This pattern supports high-throughput architectures where:

- Services can process events at their own pace.
- Workloads can be distributed across consumers.
- New services can subscribe to existing events without modifying the core system.

Because **events are immutable**, they can be safely **stored, replicated and replayed** across distributed systems without coordination or risk of data inconsistency. Immutability makes it possible to:

- Rebuild system state by replaying events.
- Scale horizontally by sharing event streams.
- Audit and debug with confidence in historical accuracy.

This makes event-based systems easier to scale, more reliable under load and more resilient to failure.
