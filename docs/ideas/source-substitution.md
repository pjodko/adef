# Source substitution for node tests

ADEF would give each upstream input at the edge of the [node graph](everything-as-a-file.md) a stable name and a configurable binding. In production, a source such as `orders` could point to a database table. A test could bind that same source to a small CSV fixture with the columns the downstream nodes expect. The nodes would run their usual logic against the fixture, using the same source reference as in production.

A test case would declare which source bindings to override, which node or set of nodes to run, and the expected output for each node it checks. ADEF would run the selected nodes and the dependencies they need, then compare the checked outputs with the expected data. A failure would identify the node and show how its output differed. Source overrides would apply only to that test run.

For example, a test could replace the production `orders` table with a CSV containing paid and cancelled orders, run the daily sales summary, and compare the result with a second CSV that contains the expected totals. Small, controlled inputs make it easy to exercise specific business rules and rerun the same checks after changes. The same mechanism can test a single transformation or several nodes working together.
