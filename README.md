# AWS Data Agent Skill

<p align="center">
  <img src="logo.svg" alt="Data Agent" width="128" height="128"/>
</p>

Governed data discovery and SQL analytics from any MCP-compatible IDE — powered
by AWS catalog services and Athena.

## What it does

Discover data assets in your AWS catalog, inspect table schemas, generate SQL
from natural language grounded in actual metadata, and execute governed queries
via Athena — all with Lake Formation permissions enforced automatically.

Works with any MCP-compatible agent: Kiro, Claude Code, Cursor, etc.

**Workflow:**
1. **Catalog discovery** — Search SageMaker Catalog (DataZone) or Glue Data Catalog
2. **Schema inspection** — Read table schemas, columns, partitions, types
3. **NL-to-SQL** — Generate SQL grounded in real metadata (no hallucinated tables)
4. **Governed execution** — Run queries via Athena with Lake Formation permissions
5. **Result presentation** — Fetch, format, and summarize query results

## Architecture

```
User (natural language)
  │
  ▼
Agent (Kiro / Claude Code / Cursor)
  │
  ├─► AWS API MCP Server  ─── or ──►  AWS MCP Server (Preview)
  │     (local, open-source)            (managed, remote)
  │     │                               │
  │     ├─► DataZone APIs               ├─► Same AWS APIs + Agent SOPs
  │     ├─► Glue APIs                   ├─► Documentation search
  │     ├─► Athena APIs                 └─► SigV4 auth via mcp-proxy-for-aws
  │     └─► Lake Formation (implicit)
  │
  └─► LLM reasoning (NL-to-SQL, analysis planning)
```

> **Which MCP server should I use?** The `awslabs.aws-api-mcp-server` (open-source,
> local) is the default and works well for this skill. The
> [AWS MCP Server (Preview)](https://docs.aws.amazon.com/aws-mcp/latest/userguide/what-is-mcp-server.html)
> is a managed remote alternative that consolidates AWS API MCP and AWS Knowledge
> MCP into a single endpoint, adding Agent SOPs and documentation search. Either
> one provides the Glue, DataZone, and Athena API access this skill needs. See
> `mcp.json` for both configurations.

## Prerequisites

- Python 3.10+ and [uv](https://docs.astral.sh/uv/getting-started/installation/)
- AWS CLI configured with credentials that have access to DataZone, Glue, Athena,
  and S3 (for Athena results)
- A DataZone domain with cataloged data assets (optional — falls back to Glue-only)
- An Athena workgroup with an S3 output location

### Required IAM Permissions

```
datazone:Search, datazone:SearchListings, datazone:GetAsset, datazone:GetListing,
datazone:ListDomains
glue:GetDatabase, glue:GetDatabases, glue:GetTable, glue:GetTables, glue:GetPartitions
athena:StartQueryExecution, athena:GetQueryExecution, athena:GetQueryResults,
athena:GetWorkGroup, athena:ListWorkGroups
s3:GetObject, s3:GetBucketLocation
```

Lake Formation permissions are enforced implicitly at query time.

## Setup

1. Copy `mcp.json` to your MCP config location:
   - Kiro: `.kiro/settings/mcp.json`
   - Claude Code: add to your MCP config

2. Choose your AWS MCP server (see comments in `mcp.json`):
   - **Option A (default):** `awslabs.aws-api-mcp-server` — open-source, runs locally via `uvx`
   - **Option B:** [AWS MCP Server (Preview)](https://docs.aws.amazon.com/aws-mcp/latest/userguide/what-is-mcp-server.html) — managed remote server, uses `mcp-proxy-for-aws` for SigV4 auth. Uncomment the `aws-mcp` block in `mcp.json` and disable the `awslabs.aws-api-mcp-server` block to avoid tool conflicts.

3. Copy the skill files:
   - Kiro: `.kiro/skills/data-agent/` (SKILL.md + references/)
   - Claude Code: `.claude/skills/data-agent/`

4. Update `AWS_REGION` in `mcp.json` to match your environment.

5. Start a conversation:
   ```
   "What data do we have in the catalog?"
   "Show me tables related to customer transactions"
   "What's the average order value by region for Q1 2026?"
   "Find all tables in the analytics database and describe their schemas"
   ```

## Limitations

- **SQL only** — no PySpark or Python code generation
- **Read-only** — no data mutation queries
- **Athena engine** — uses Athena (Presto/Trino), not serverless Spark
- **No notebook context** — doesn't read IDE/notebook state

## How it compares to SageMaker Data Agent

| Capability | SageMaker Data Agent | This Skill |
|---|---|---|
| Catalog discovery | ✅ DataZone + Glue | ✅ DataZone + Glue (via AWS API MCP or AWS MCP Server) |
| Business metadata | ✅ Glossary, descriptions | ✅ Same APIs |
| Schema awareness | ✅ Full table schemas | ✅ Same APIs |
| NL-to-SQL | ✅ Built-in | ✅ LLM-generated, grounded in schema |
| Query execution | ✅ Serverless Spark + Athena | ⚠️ Athena only |
| Governance | ✅ Lake Formation + IAM | ✅ Same (enforced at Athena layer) |
| Python/PySpark | ✅ Full support | ❌ SQL only |
| Notebook context | ✅ Reads notebook state | ❌ No notebook |
| Available outside SMUS | ❌ SMUS only | ✅ Any MCP client |
| Glue-only fallback | ❌ Requires DataZone | ✅ Works without DataZone |

## Background

Amazon SageMaker Data Agent is a built-in AI assistant inside SageMaker Unified
Studio that provides context-aware data analysis. It reads the Glue Data Catalog
and DataZone business catalog metadata, then generates and executes
SQL/Python/PySpark code. However, Data Agent is only available inside SMUS — no
API or MCP exposure exists for external agents.

This skill brings the same core workflow (catalog discovery → schema inspection →
NL-to-SQL → governed execution) to any MCP-compatible environment by chaining
publicly available AWS APIs through the AWS API MCP Server or the newer
[AWS MCP Server (Preview)](https://docs.aws.amazon.com/aws-mcp/latest/userguide/what-is-mcp-server.html).

## License

Apache-2.0
