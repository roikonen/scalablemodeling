# Start Modeling From Events

To design reliable scalable systems, we need to start from **temporal** thinking (the **flow of time** and how 
things evolve) and gradually move into **spatial** thinking (the **arrangement** of things). In essence, we design 
spatial systems to handle temporal matters: **events**.

**Services are rooted in space but act in time**: At any given moment, a service's location is tied to a physical or 
virtual environment (e.g. a server, container or cluster). Over time, services evolve as they process events, make 
decisions and produce outputs.

**Events are rooted in time but act in space**: Events are fixed to the moment they are created, carrying an 
immutable snapshot of information. Often, events are related to space in addition to time, but due to their 
immutability, their replicated 'echoes' move between services.

![type:video](https://www.youtube.com/embed/QXCLfQrqbiI)

So, instead of focusing too much on the **space** between services, consider the **flow of events** and how services
evolve over **time**.

The **immutability of events** contributes significantly to scalability, particularly in event-driven architectures.
Additionally, **events** play a central role in uncovering domain insights and fostering a shared language.
**Thus, we adopt an [event-centric](theory/#event-centrism) approach**.

# Event Centrism

In **Event Centrism**, everything we experience is either an event or a trace of one. Reality is not made up of *static objects* but a continuous flow of occurrences where everything from a falling leaf to a mountain is part of an ongoing process. Even seemingly **permanent things** are *temporary outcomes* of past events, always subject to change.

Our lives are **driven by events**. Every thought, action and emotion is triggered by something and memories are a collection of past events that define *who we are*. Traces of past events like an old building or a weathered book remind us of what once occurred, continuing to shape the present.

Events can also be *potential*, waiting to happen, or *hidden*, unfolding beyond our perception like biological processes or distant cosmic phenomena. Every event is part of a **cause-and-effect continuum**, influencing future occurrences. **Time**, in this view, is meaningful only as the medium through which events unfold.

Even the **self** is an ongoing event, constantly shaped by experiences and interactions. In *Event Centrism*, everything in life is *fluid, dynamic and interconnected*, emphasizing that our world and our identities are in constant motion, driven by the events we experience.

**Event Centrism** is not an *absolute truth* but rather one way to understand the world, a very useful way to *model systems*.
