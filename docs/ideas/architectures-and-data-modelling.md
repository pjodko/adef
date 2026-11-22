# Planned architectures and data modelling

## Layered architecture

ADEF would support layered architectures such as Medallion, where bronze holds raw source data, silver holds cleaned and conformed data, and gold serves business-facing datasets. Layers give teams a way to make the stages of refinement explicit and reuse intermediate datasets across downstream models.

## Dimensional modelling

ADEF would support modelling data as facts and dimensions. A dimensional model could serve as part of the gold layer in a Medallion architecture.

- **Slowly changing dimensions (SCD) Type 2.** Provide optimised implementations that create a new dimension row when tracked attributes change and retain previous rows with their validity periods. This preserves the dimension values that applied at a given point in time.
- **Business key to surrogate key substitution.** When loading a fact, use its source business key and event time to resolve the surrogate key of the dimension row valid at that time. This lets historical facts join to the relevant dimension version.
- **Fact table historisation.** Support retaining prior versions of facts when source values change so teams can reconstruct earlier states of the fact table. The versioning strategy and timestamp semantics remain open.
