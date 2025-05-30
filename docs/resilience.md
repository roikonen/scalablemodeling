![](assets/images/resilience_responsiveness.png#only-light)
![](assets/images/resilience_responsiveness_dark.png#only-dark)

Success demands systems that are both **resilient to failures** and **responsive at all times**. Both of these qualities depend on **scalability**, especially in systems expected to grow.

- **Resilience** is the system’s ability to **recover from failures** and continue operating ideally without data loss or service downtime.
- **Responsiveness** is about **how quickly a system reacts** to user actions or requests, maintaining consistent performance under varying load. While **availability** is part of this, it's not the whole story. A system can be “available” but still frustratingly slow. We’ve all stared at spinners. What we want isn’t just availability but responsiveness.

A non-scalable system might perform well under low load but as usage grows it can quickly become unresponsive or prone to failure, directly undermining both resilience and responsiveness.

- **Scalability supports resilience** by allowing systems to redistribute load, isolate failures and add resources to minimize the risk of total failure.
- **Scalability supports responsiveness** because it enables **elasticity**[^1]. When traffic spikes, an elastic system can spin up additional application servers, database replicas and other resources. This prevents latency bottlenecks and helps maintain fast consistent response times even under pressure.

[^1]: **Elasticity** is the *runtime expression* of scalability. It’s the system’s ability to **automatically scale up or down** in response to current demand.

With Scalable Modeling, models enable systems that are **resilient** and **responsive** by design.
