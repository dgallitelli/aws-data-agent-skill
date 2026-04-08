# Athena Query Execution

## Start query

Use the AWS MCP tool to call `athena start-query-execution`:
- `--query-string "<SQL>"`
- `--work-group <WORKGROUP>`
- `--query-execution-context Database=<DATABASE>`

Extract the `QueryExecutionId` from the response.

## Check status

Use the AWS MCP tool to call `athena get-query-execution`:
- `--query-execution-id <EXECUTION_ID>`

Check `Status.State`:
- `QUEUED` or `RUNNING` → check again
- `SUCCEEDED` → proceed to retrieve results
- `FAILED` → read `Status.StateChangeReason` and handle error
- `CANCELLED` → inform the user

## Retrieve results

Use the AWS MCP tool to call `athena get-query-results`:
- `--query-execution-id <EXECUTION_ID>`
- `--max-results 50`

Note: `get-query-results` returns max 1000 rows per call. If the result set is
larger, use the `NextToken` from the response to paginate.

## Present results

- Formatted table (first 20-50 rows)
- Summary statistics if applicable (row count, min/max/avg for numeric columns)
- Data scanned (`Statistics.DataScannedInBytes`) and execution time
  (`Statistics.EngineExecutionTimeInMillis`) from the `get-query-execution`
  response

## Error handling

### Access Denied (Lake Formation)
- Explain that Lake Formation governance is blocking access
- Suggest the user request access through their SageMaker Catalog subscription
  workflow or contact their data domain owner
- Do NOT suggest workarounds that bypass governance

### Syntax Error
- Fix the SQL and ask user to approve the corrected version (loop back to SQL
  generation)

### Table Not Found
- The catalog listing may be stale or the table may have been deleted
- Suggest re-searching the catalog or checking with the data owner

### Query Timeout
- Suggest adding partition filters or LIMIT clauses
- Provide the QueryExecutionId so the user can check status later