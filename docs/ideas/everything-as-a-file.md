# Everything as a file

ADEF would treat each file as a callable node. Writing `|filename.ext` invokes the exact file named, including its extension. ADEF would infer a node’s parameters from the `$name` occurrences in its body. Repeated uses of the same name refer to the same input. This requires no parameter header or front matter.

ADEF uses *node* because SQL queries, data files, prompts, and compositions all share the same way of being called by filename.

- **SQL files.** A node can contain standard SQL, optionally parameterized. For example, `orders_by_status.sql` could contain `SELECT * FROM orders WHERE status = $status`. Its caller supplies `status`.
- **Data files.** Calling a file such as `orders.csv` returns its rows as a table. Its columns follow the definitions in the shared column repository, and their values must satisfy the corresponding constraints. [Shared column repository](shared-column-repository.md)
- **Prompt files.** A prompt is a first-class node. A prompt containing “Summarize the reasons for returns in $orders” takes an inferred `orders` input. A composition can call the prompt and pass its response to another node.
- **Composition files.** A composition calls other files and binds their inputs. The composition is itself a callable node, so a caller can reuse the whole operation by filename.

## Counting orders by status

Suppose `count_rows.sql` contains `SELECT COUNT(*) FROM $table`. A composition named `count_orders_by_status.comp` could supply the filtered orders as its table input.

```text
|count_rows.sql
  table=|orders_by_status.sql status=$status
```

The indentation attaches the `table` binding to `count_rows.sql`. The inner call returns the rows matching the supplied status. That result becomes the outer query’s `$table`, so the composition returns a count of the matching orders.

Calling `|count_orders_by_status.comp status='paid'` supplies a literal. Inside the composition, `status=$status` forwards that value to `orders_by_status.sql`. Another composition could instead write `|count_orders_by_status.comp status=$status`, exposing the same input to its own caller. Writing `status='paid'` directly on the inner call would fix the filter there and remove the counting composition’s only parameter.

Jinja could optionally generate additional SQL, for example a repeated set of select expressions. Input inference and binding would work independently of Jinja.
