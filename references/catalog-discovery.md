# Catalog Discovery

## With DataZone (SageMaker Catalog)

### Keyword search

Use the AWS MCP tool to call `datazone search-listings`:
- `--domain-identifier <DOMAIN_ID>`
- `--search-text "<user's search terms>"`
- `--max-results 10`

For each result, extract: asset name, description, glossary terms (business
context), owning project, asset type (table, file, etc.).

### Browse all assets

Use the AWS MCP tool to call `datazone search`:
- `--domain-identifier <DOMAIN_ID>`
- `--search-scope ASSET`
- `--max-results 20`

### Get asset details

Use the AWS MCP tool to call `datazone get-asset`:
- `--domain-identifier <DOMAIN_ID>`
- `--identifier <ASSET_ID>`

## Without DataZone (Glue-Only Fallback)

If no DataZone domain exists, fall back to Glue Data Catalog directly.

### List databases

Use the AWS MCP tool to call `glue get-databases`:
- `--max-results 50`

### List tables in a database

Use the AWS MCP tool to call `glue get-tables`:
- `--database-name <DATABASE>`
- `--max-results 50`

### Present results

For each discovered asset, present:
- Asset name
- Description / business context (if available)
- Database and table name
- Column count and key columns if available

⏸ Ask the user which asset(s) they want to explore or query.