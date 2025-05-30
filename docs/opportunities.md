# Opportunities

## The Three Dimensions to Scalability

In this section we are in the context of:

* Event-Driven Architecture (EDA)
* Command Query Separation (CQS)
* Command Query Responsibility Segregation (CQRS)

If these are not familiar to you, please refer to [theory](../theory/) section. 

**Scale Cube**

![](assets/images/scale_cube.png#only-light)
![](assets/images/scale_cube_dark.png#only-dark)

### Decomposition

![](assets/images/decomposition.png#only-light)
![](assets/images/decomposition_dark.png#only-dark)

!!! quote

    "Developers simply do not implement large scalable applications assuming distributed transactions...
    Two-phase commit is the anti-availability protocol."

    **_Pat Helland_**

### Duplication

![](assets/images/duplication.png#only-light)
![](assets/images/duplication_dark.png#only-dark)

### Partition

![](assets/images/partition.png#only-light)
![](assets/images/partition_dark.png#only-dark)