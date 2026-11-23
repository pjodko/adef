# Planned architectures and data modelling

## Layered architecture

ADEF would support layered architectures such as Medallion. Only the first (the most upstream) layer allows column and table names not following the convention.

## Dimensional modelling

ADEF would support modelling data as facts and dimensions. A dimensional model could serve as the gold layer in a Medallion architecture.

Required features:

- **Slowly changing dimensions (SCD) Type 2.** Provide optimised implementations that create a new dimension row when tracked attributes change and retain previous rows with their validity periods. This preserves the dimension values that applied at a given point in time.
- **Business key to surrogate key substitution.** When loading a fact, use its source business key and event time to resolve the surrogate key of the dimension row valid at that time. This lets historical facts join to the relevant dimension version.
- **Fact table historisation.** Support retaining prior versions of facts when source values change so teams can reconstruct earlier states of the fact table. The versioning strategy and timestamp semantics remain open.
