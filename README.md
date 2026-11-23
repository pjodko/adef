© 2026 Piotr Jodko

# adef
Agentic Data Engineering Framework

## Why I’m building ADEF

ADEF grew out of years of working with clients and seeing different data teams struggle with many of the same problems. I started with those problems and designed the framework from the ground up.

The goal is to enable small and medium business to build solid data platform without much cost, work and maintenance. The tradoff is the opinionated/idiomatic approach ("this is how you should do it, do not reinvent").

dbt is the closest point of comparison. My goal is to build something better for the teams I’ve worked with by designing ADEF around how people and agents can build and maintain data pipelines together.

The announcement that Fivetran and dbt Labs were merging was one of the things that prompted me to start thinking seriously about building ADEF. [Merger announcement](https://www.fivetran.com/press/fivetran-and-dbt-labs-unite-to-set-the-standard-for-open-data-infrastructure-2025)



## ADEF is not...

- not for large data - single digit TBs
- not for very fast data - no ambition for real time. 

## Principles

- Things are defined once and reused
- Idiomacy over freestyle
- Frequency of data refresh is function of compute, not architecture.
- Everything as a file.
- Code first, testing first
- Agentic first, human auditable.


## Supported databases

- PostgreSQL
- Azure SQL

Planned:
- duckDB
- Big Query
- Snowflake
- Apache Spark SQL

## Inspirations

- dbt
- pyspark
- Swift programming language - I like how Swift reduces any repetition of code, inferes things so they don't have to be written. It may matter less (or more) with genAI coding.



## Ideas

- [Shared column repository](docs/ideas/shared-column-repository.md)
- [Column relationships](docs/ideas/column-relationships.md)
- [Everything as a file](docs/ideas/everything-as-a-file.md)
- [Source substitution for node tests](docs/ideas/source-substitution.md)
- [Planned architectures and data modelling](docs/ideas/architectures-and-data-modelling.md)
