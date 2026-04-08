# Schema Inspection

## Get table schema

Use the AWS MCP tool to call `glue get-table`:
- `--database-name <DATABASE>`
- `--name <TABLE_NAME>`

Extract and present:
- All columns with names, types, and comments
- Partition keys
- Table location (S3 path)
- SerDe and input format (Parquet, CSV, JSON, etc.)
- Row count / size if available from table parameters

## Multi-table queries

If the user's question involves multiple tables, repeat for each table and
identify potential join keys by matching column names and types.

## Large databases

For databases with many tables, list all tables first using `glue get-tables`
with `--database-name <DATABASE> --max-results 50`, then let the user select
which tables are relevant.

## Confirmation

⏸ Present the discovered schema to the user and confirm: "I found these tables
and columns. Does this look right before I generate SQL?"