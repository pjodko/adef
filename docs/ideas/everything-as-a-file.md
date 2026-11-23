# Everything as a file

ADEF would treat each file as a callable node. Writing `|filename.ext` invokes the exact file named. 

ADEF uses *node* because SQL queries, data files, prompts, and compositions all share the same way of being called by filename.

- ADEF would infer a node’s parameters from the `$name` occurrences in its body recursivelly. Repeated uses of the same name refer to the same input. 
- Node's minimalistic header can have optional fields:
  - description
  - materialization (table/view name, ..., mode: overwrite, append, SCD2, incremental, ...)
  - output schema

Node types:

- **SQL files.** A node can contain standard SQL, optionally parameterized. For example, `orders_by_status.sql` could contain `SELECT * FROM orders WHERE status = $status`. Its caller supplies `status`.
- **Data files.** Calling a file such as `orders.csv` returns its rows as a table. Its columns follow the definitions in the shared column repository, and their values must satisfy the corresponding constraints. [Shared column repository](shared-column-repository.md)
- **Prompt files.** A prompt is a first-class node. A prompt containing “Summarize the reasons for returns in $orders” takes an inferred `orders` input. A composition can call the prompt and pass its response to another node.
- **Composition files.** A composition calls other files and binds their inputs. The composition is itself a callable node, so a caller can reuse the whole operation by filename.

The node files can be located anywhere in the reachable files system. For brief notation, the default folder is to be specified.

### Prompt nodes

The prompt describes transformation and the framework generates the code based on the context. The generation have to be stored so they can be reused when the same context is identified.

## Counting orders by status

Suppose `count_rows.sql` contains `SELECT COUNT(*) FROM $table`. A composition named `count_orders_by_status.comp` could supply the filtered orders as its table input.

```text
|count_rows.sql
  table=|orders_by_status.sql status=$status
```

The indentation attaches the `table` binding to `count_rows.sql`. The inner call returns the rows matching the supplied status. That result becomes the outer query’s `$table`, so the composition returns a count of the matching orders.

Calling `|count_orders_by_status.comp status='paid'` supplies a literal. Inside the composition, `status=$status` forwards that value to `orders_by_status.sql`. Another composition could instead write `|count_orders_by_status.comp status=$status`, exposing the same input to its own caller. Writing `status='paid'` directly on the inner call would fix the filter there and remove the counting composition’s only parameter.

Jinja could optionally generate additional SQL, for example a repeated set of select expressions. Input inference and binding would work independently of Jinja.

## Forwarding inputs through a composition (proposition)

When only one nested call exposes a required input named `input_table`, the enclosing composition inherits that parameter. The same rule applies at each level, so the outer caller can supply the table once without each intermediate node having to forward it explicitly.

When two branches expose the same unbound name, ADEF would report the ambiguity before execution. The author must bind the input on both calls at the level where the branches meet. Two branches can accept the same kind of table and still need different data, so sharing requires an explicit choice.

For example, suppose both month selectors take an order summary as their `in` parameter. To summarize the same table for both branches, `compare_orders.comp` would explicitly forward its own `input_table` twice:

```text
|compare_summaries.sql
  current_summary=|select_current_month.sql
    in=|summarize_orders.comp input_table=$input_table
  prior_summary=|select_prior_month.sql
    in=|summarize_orders.comp input_table=$input_table
```

The caller supplies one `input_table`. To accept separate tables, the author writes `input_table=$current_table` on the first summary call and `input_table=$prior_table` on the second. The composition then exposes `current_table` and `prior_table`. Either name can pass through an enclosing node implicitly while it remains unique there.
