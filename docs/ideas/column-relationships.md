# Column relationships

ADEF would declare relationships between columns defined in the [shared column repository](shared-column-repository.md). Each declaration describes a business fact and the constraints that apply to it. Teams can record that meaning before deciding how to group the columns into tables or other structures.

Focusing on actuall columns and binding their relationships with business meaning immediately gives practical implications:

- data testing
- users and agents navigate data with understanding.

## One concept, different constraints

A relationship names a business fact, identifies the columns that participate, and can specify constraints such as how many values may participate on each side.

| Business fact | Columns | Constraint |
| --- | --- | --- |
| A user has a date of birth | `user_id`, `date_of_birth` | A given user ID determines at most one date of birth. |
| A user purchased a product | `user_id`, `product_id` | Many-to-many. A user can purchase several products, and several users can purchase the same product. |
| An order was placed by a user | `order_id`, `user_id` | Each order has exactly one user, and a user can place many orders. |

The order example connects two identifiers and also carries a functional dependency. If two records refer to the same order, they must agree on the user. Requiring every order to have a user is a separate constraint from requiring records for the same order to agree on who that user is.

Relationships between identifiers can express the business meaning and cardinality familiar from entity–relationship diagrams. The name matters because the same identifiers can participate in several relationships. For example, a user can view a product or purchase it, and each relationship describes a different fact.

## Connection to relational theory

The date-of-birth example is a functional dependency of the kind used in third normal form and Boyce–Codd normal form. Fifth and sixth normal forms deal with join dependencies and provide related ideas about decomposing relations. ADEF’s proposal starts one step earlier by making the business dependencies explicit before settling on a relational layout. [Fagin on projection–join normal form](https://research.ibm.com/publications/normal-forms-and-relational-database-operators) · [Date on database design and relational theory](https://link.springer.com/book/10.1007/978-1-4842-5540-7)
