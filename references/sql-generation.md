# SQL Generation Rules

## Grounding

- Use **only** table and column names that exist in the schema discovered in the
  previous step. Never hallucinate table or column names.
- Use the correct `database.table` notation for Athena.
- Respect column types — do not compare strings to integers, etc.

## Performance

- Use partition columns in WHERE clauses when possible.
- Use LIMIT for exploratory queries unless the user asks for full results.
- Prefer COUNT/SUM/AVG aggregations over SELECT * for large tables.

## Syntax

- Use **Presto/Trino SQL syntax** (Athena engine v3).
- Add comments explaining the query logic.
- For cross-database joins, use fully qualified `database.table` names.

## Presentation

Present the generated SQL to the user with:
- The SQL query (formatted and commented)
- Which tables and columns it uses
- What the query does in plain language
- Any assumptions made

⏸ **Wait for user approval before executing.** The user may want to modify the
query.