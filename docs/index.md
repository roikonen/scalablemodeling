# ![](assets/logo_100x100.png){ width="30" } What's Scalable Modeling?

This page introduces a **modeling technique** for designing **scalable systems**. Any system aiming for growth should be designed with scalability in mind, as **early consideration of scalability is often essential for long-term success**. Scalable Modeling helps to design in a way that **enables future growth**, even if you're not scaling yet.

With **Scalable Modeling**, you can embed scalability into your system design from the beginning. Influenced by [Clean Architecture](credits/#robert-c-martin), [Event-Driven Architecture](theory/#event-driven-architecture-eda) (EDA), [Domain-Driven Design](credits/#eric-evans) (DDD), [EventStorming](credits/#alberto-brandolini), and [CQRS](theory/#command-query-responsibility-segregation-cqrs), this approach remains flexible and avoids rigid adherence to any one methodology. That’s why this **event-centric architectural approach** has a distinct name: [CEQS – Command-Event-Query Separation](ceqs/#ceqs-command-event-query-separation).

**Your implementation doesn’t need to scale from day one.** But when the model is designed with scalability in mind and follows [CEQS](ceqs/#ceqs-command-event-query-separation), the system can evolve, adapting to rising demands for resilience and responsiveness, so that **growth becomes an advantage rather than a challenge**.


![](assets/images/scalable_modeling_components.png#only-light)
![](assets/images/scalable_modeling_components_dark.png#only-dark)
_Justification for the red arrows in sections: [Queries](components/#queries) & [Time Travel](challenges/#time-travel)._

**Webinar recording: [Software engineering for the future: Fast, scalable and built to last](https://www.twoday.fi/en/content/webinars/webinar-software-engineering-for-the-future-fast-scalable-and-built-to-last) (November 26, 2024).**

## High Level Overview

There are **three opportunities** and **three challenges** in scalability and understanding them takes you far already. Combining the opportunities & challenges with an **upfront and iterative modeling technique** provides a solid foundations for modeling reliable and scalable systems.

### Three Opportunities

1. **[Decomposition](opportunities/#decomposition)** - scale by splitting different things
2. **[Duplication](opportunities/#duplication)** - scale by cloning
3. **[Partition](opportunities/#partition)** - scale by splitting similar things

**Immutability** (of messages/events) plays key role in each aspect. 

### Software Engineering Flow

Software is ultimately a **model** — a conceptual solution that, while invisible, solves real-world challenges. 
In software engineering, three aspects are critical:

1. **Understanding**: **WHY** the software is needed and by who (understanding the **purpose** and the **problem**
   it should address).
2. **Designing**: **WHAT** is the **conceptual model** for the solution.
3. **Developing**: **HOW** the solution is **implemented**.

![](assets/images/why_what_how.png#only-light)
![](assets/images/why_what_how_dark.png#only-dark)

[Scalable Modeling](https://roikonen.github.io/scalablemodeling/) serves as an opinionated bridge from **WHY** to
**HOW**, with a primary focus on the **WHAT**.

Iteration is naturally much cheaper when it is done on the conceptual model rather than on the implementation level.

### Three Challenges

1. **[Deduplication](challenges/#deduplication)** - as exactly-once delivery is impossible in distributed systems
2. **[Tailoring Consistency](challenges/#tailoring-consistency)** - as strong consistency is the wrong default
3. **[Time Travel](challenges/#time-travel)** - as distribution causes eventual consistency

## Summary of Components

![](assets/images/components_of_scalable_modeling.png#only-light)
![](assets/images/components_of_scalable_modeling_dark.png#only-dark)

## License For Using the Pictures

![](assets/images/copyright.png#only-light){ width="200" }
![](assets/images/copyright_dark.png#only-dark){ width="200" }

See [License](https://github.com/roikonen/scalablemodeling/blob/main/LICENSE.md).

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
