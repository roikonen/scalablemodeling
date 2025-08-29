---
date:
  created: 2025-08-08
slug: how-to-stop-paying-for-features-nobody-uses
---

# Software’s Real Cost Is Building Wrong Features

Every year companies spend millions building software that looks right in a demo but collects dust in production. The issue is not lazy engineers or indecisive stakeholders. It is a gap in understanding between the people who know the problem and the people who write the code.

This perspective draws on both global studies and recent Nordic research to show why teams ship features nobody uses and how to stop it.

The result: bloated backlogs, features that miss the mark and a steady drain on budget. An often-cited figure from an early 2000s Standish Group report suggests that around **64% of features** in delivered software saw little or no use[^1]. While the exact percentage varies between studies, the underlying pattern that many features go unused remains widely observed. If you are funding those features you are paying for waste.

The good news is that many of the most expensive misunderstandings can be addressed before the first line of code is written. Coding will still raise new questions but with a shared understanding in place those iterations are faster, cheaper and far less painful.

## Why This Problem Hurts the Bottom Line

Misunderstood requirements create a chain reaction of cost overruns and missed value.

- NASA’s own analysis shows that fixing a requirements defect during design can be **3× to 8×** more expensive than catching it in the requirements phase. In integration and test phases the cost jumps to **21× to 78×**, and in extreme cases (when found in production) it can exceed **1 500×**[^2]
- Research by Barry Boehm and others has shown that **30–50% of development effort** is often spent on avoidable rework, much of it caused by misunderstood or incomplete requirements[^3]
- In a Nordic study, engineers spent over **16 hours per week** in meetings, much of it dealing with misunderstandings that could have been avoided through earlier shared understanding[^4]
- A Swedish multi-company study found that weak alignment between requirements work and testing leads to rework, quality defects and delivery delays even when stakeholders believe they have a shared understanding[^5]

These are not just statistics. They are your budget leaking out of the project one misunderstanding at a time.

## How the Gap Opens

![20250808-1.png](images/20250808-1.png#only-light)  
![20250808-1-dark.png](images/20250808-1-dark.png#only-dark)

When a project jumps from **Purpose & Problem** (*Why*) straight to **Implementation** (*How*), there is no shared bridge between the two.

Domain experts focus on why the software is needed and who needs it. Engineers focus on how it will be implemented.

Without a shared middle ground, intent is often lost. The result is software that works technically but fails to solve the real problem, leading to expensive rework and wasted features.

In the Nordic study, many of those **16 hours per week** spent in meetings were used to realign teams after these gaps emerged. Instead of moving work forward, time was spent repairing shared understanding that should have been built before implementation began[^4].

The Swedish study showed a similar pattern. Requirements and testing teams often worked from different assumptions, lacked traceability between specifications and verification steps and coordinated too late. This fragmented approach slowed delivery and created duplicate effort[^5].

## Closing the Gap

!!! quote

    "There is nothing so useless as doing efficiently that which should not be done at all."

    _**Peter F. Drucker**_

![20250808-2.png](images/20250808-2.png#only-light)  
![20250808-2-dark.png](images/20250808-2-dark.png#only-dark)

The missing link is the **Conceptual Model** (*What*).

This is the shared understanding of how the solution will address the purpose and problem. It is built collaboratively between domain experts, engineers, architects and designers before implementation begins.

By making the **Conceptual Model** explicit, teams ensure:

- The purpose is understood
- The scope is clear
- The transition from concept to implementation is smooth

## Why Early Alignment Saves Money

![20250808-3.png](images/20250808-3.png#only-light)  
![20250808-3-dark.png](images/20250808-3-dark.png#only-dark)

Iteration cost is low when teams adjust at the **Conceptual Model** stage. Feedback at this level is fast, inexpensive and easy to act on.

Once a project moves into **Implementation**, changes become far more expensive and slow to deliver.

Early alignment does not remove the need for iteration. It ensures that when new questions or discoveries come up during coding they can be addressed faster, with fewer side effects and at a much lower cost.

Validating the **Purpose & Problem** and the **Conceptual Model** early gives teams:

- Cheaper changes
- Faster learning cycles
- Fewer costly surprises in delivery

## Tools That Close the Gap

### Domain-Driven Design (DDD)
DDD is a way to embed the business language directly into the design and code.

- **Ubiquitous Language** locks meaning to agreed terms
- **Bounded Contexts** prevent rules from bleeding across domains
- **Collaborative modeling** ensures experts stay involved until the intent is clear

These practices also address one of the Swedish study’s key findings that closer, earlier collaboration between requirements and testing roles improves alignment and reduces wasted effort[^5].

### Event Storming
A fast, visual workshop where engineers, architects, designers and experts map business events together.

- Reveals hidden complexity in hours
- Surfaces misunderstandings before coding begins
- Creates shared ownership of the solution

## The Payoff

Addressing the expert–engineer gap is not a nice-to-have. It is a cost-control measure.

Teams that invest in collaborative domain exploration:

- Deliver features that get used
- Reduce rework and budget overruns
- Build systems that align with the real business

## Final Word

!!! quote

    "It's developer (mis)understanding that's released in production, not the experts' knowledge."

    _**Alberto Brandolini**_

Software is translation. Every mistranslation costs money. **DDD** and **Event Storming** will not remove all project risk but they make misunderstandings expensive to ignore and cheap to fix.

If you are tired of paying for features nobody uses bring your experts and engineers into the same room before the first sprint. The savings start before the code does.

[^1]: Standish Group (2002). *CHAOS Report* figure cited by Mike Cohn in [LinkedIn article](https://www.linkedin.com/pulse/64-features-really-rarely-never-used-mike-cohn). This is an older data point used here as an illustration of the problem, not a current benchmark.  
[^2]: NASA (2010). *Cost of Fixing Defects*. Based on analysis of NASA software projects and multiple independent studies. Relative cost multipliers: 3×–8× in design, 7×–16× in build, 21×–78× in integration/test, and up to 1 500× in extreme post-deployment cases. [PDF](https://ntrs.nasa.gov/api/citations/20100036670/downloads/20100036670.pdf)  
[^3]: Boehm, B., & Papaccio, P. N. (1988). *Understanding and Controlling Software Costs*. IEEE Transactions on Software Engineering, 14(10), 1462–1477. doi:[10.1109/32.6191](https://doi.org/10.1109/32.6191)  
[^4]: Stray, V. G., & Moe, N. B. (2020). *Understanding Coordination in Global Software Engineering: A Mixed-Methods Study on the Use of Meetings and Slack*. arXiv:2007.02328. [PDF](https://arxiv.org/pdf/2007.02328.pdf)  
[^5]: Bjarnason, E., Rasmusson, A., Unterkalmsteiner, M., Engström, E., & Gorschek, T. (2023). *Challenges and Practices in Aligning Requirements with Verification and Validation: A Case Study of Six Companies*. arXiv:2307.12489. [PDF](https://arxiv.org/pdf/2307.12489.pdf)  
