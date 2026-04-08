---
name: data-agent
description: >
  Discovers governed data assets in AWS catalogs (DataZone/Glue), generates SQL
  from natural language grounded in actual schemas, and executes governed queries
  via Athena with Lake Formation permissions. Use when the user says "find data",
  "query the catalog", "what tables do we have", "analyze this dataset", or asks
  about data in their lakehouse.
metadata:
  version: "0.1.0"
---

# Data Agent

Governed data discovery and SQL analytics for any MCP-compatible agent.

## Scope

**Supported:**
- Discovering data assets in SageMaker Catalog (DataZone) or Glue Data Catalog
- Generating SQL queries from natural language grounded in actual table schemas
- Executing queries via Athena with Lake Formation governance
- Retrieving and summarizing query results

**Not supported:**
- PySpark or Python code generation (SQL only)
- Data writing or mutation (read-only queries)
- Cross-region catalog federation

## Principles

1. **Discover before querying.** Always search the catalog first to ground SQL in real metadata.
2. **Show your work.** Display tables, columns, and types before generating SQL.
3. **Confirm before executing.** Show generated SQL and wait for approval.
4. **Respect governance.** Never bypass Lake Formation. If access is denied, explain what's missing.
5. **One step at a time.** Each response advances exactly one step.

## Workflow

### Step 1: Discover Configuration

Read `references/configuration.md` and follow its instructions to discover or
confirm the DataZone domain, Athena workgroup, and output location.

If no DataZone domain exists, the skill operates in Glue-only mode.

⏸ Confirm configuration with the user before proceeding.

### Step 2: Catalog Discovery

Read `references/catalog-discovery.md` and follow its instructions.

Use DataZone APIs if a domain is configured, otherwise fall back to Glue-only
discovery. Present discovered assets to the user.

⏸ Ask the user which asset(s) they want to explore or query.

### Step 3: Schema Inspection

Read `references/schema-inspection.md` and follow its instructions.

Fetch the full schema for the selected table(s) from Glue Data Catalog. For
multi-table queries, identify potential join keys.

⏸ Confirm the discovered schema with the user before generating SQL.

### Step 4: Generate SQL

Read `references/sql-generation.md` and follow its rules.

Generate a SQL query grounded in the actual schema from Step 3. Present the
query with an explanation of what it does and any assumptions made.

⏸ **Wait for user approval before executing.**

### Step 5: Execute and Present Results

Read `references/athena-execution.md` and follow its instructions.

Execute the approved query via Athena, poll for completion, retrieve results,
and present them with summary statistics and cost information.

If the user wants to continue analyzing, loop back to Step 4 with the new
question, maintaining context about previously discovered schemas.

## Troubleshooting

### No DataZone domain found
The account may not have SageMaker Catalog / DataZone configured. Fall back to
Glue-only discovery — see `references/catalog-discovery.md`.

### Access Denied on query execution
Lake Formation is enforcing governance. See error handling in
`references/athena-execution.md`. Do NOT suggest workarounds.

### Empty catalog results
The search terms may be too specific, or the user may not have subscription
access to the relevant DataZone project. Suggest broadening the search or
checking with the data domain owner.