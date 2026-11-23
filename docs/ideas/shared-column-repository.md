# Shared column repository

Several dbt models can use the same business column. Teams can end up repeating its description and quality checks in each dbt model, creating extra work and room for those definitions to drift.

ADEF would keep one shared column repository per project or platform implementation. Each [node](everything-as-a-file.md) that returns tabular data would use a subset of the columns defined there. The node defines how it returns its data. The column repository defines what each column means and supplies its defaults wherever it’s used. The goal is to store as much metadata as possible with a column.

- **Meaning and aggregation.** A column definition includes:
  - a business description 
  - a classification as a measure or an attribute. For example, `account_value_original` could be a measure defined as the account value in its original currency. 
  - type
  - its default aggregation functions, attributes allowed to agregate across, required for aggregation or not allowed to aggregate over.
  - semantic calculations: `avg_arr_by_region` - formula what to calculate over which attributes, additional conditions. So, it servers as what is called "semantic layer" today.
  - **Data quality.** Define shared checks once on the column. For example, an age column could have an illustrative upper bound of 200. Nodes using that column inherit the check.
- **Exceptions.** ADEF would let teams override a default in a specific context. In my experience, those exceptions are surprisingly rare. Shared defaults reduce repetitive work and keep definitions consistent in the common case.

## Catching duplicate definitions upfront

Developers and agents start with the shared column repository when adding columns to a node. If either proposes a new column, an AI agent checks its name and business description against existing definitions before it’s added. For example, it could flag `acct_val_orig` as a likely duplicate of `account_value_original` and suggest reusing the existing column. The AI check makes it practical to spot the same business meaning under different names. The shared definition can apply across nodes even when their actual values differ.

## Schemas and node outputs

A named schema selects columns from the shared column repository. Its definition can mix individual columns and other named schemas. For example, if `customer` and `payment` are predefined schemas, an order summary could be written as follows.

```text
order_summary = [customer], order_id, order_date, [payment], status
```

The summary includes the columns selected by `customer` and `payment` along with the three columns listed directly. A node can use `order_summary` as its output schema or define its own output schema by combining predefined schemas and repository columns in the same way. 

Open topics:

- Whether a node’s output schema can be determined dynamically.
- How to deal with conflicts in composed schema, eg. both `customer` and `payment` come with column `last_status` (which actually is a wrong, too generic name) - should we take the first occurance, automatically rename (nope), or raise an error?

## Exceptions

Only the upsteam-most layer can ignore centralized naming and follow sources' column and table names.