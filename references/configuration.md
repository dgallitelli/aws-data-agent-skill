# Configuration Discovery

If the user hasn't provided configuration values, discover them using the AWS
MCP tool.

## DataZone Domain

Use the AWS MCP tool to call `datazone list-domains`.

Extract the `id` of the relevant domain. If multiple domains exist, ask the user
which one to use. If no domains exist, this account does not have DataZone
configured — fall back to Glue-only discovery (see `catalog-discovery.md`).

## Athena Workgroup

Use the AWS MCP tool to call `athena list-work-groups`.

Use the `primary` workgroup unless the user specifies otherwise.

## Athena Output Location

Use the AWS MCP tool to call `athena get-work-group --work-group <WORKGROUP>`.

Extract `Configuration.ResultConfiguration.OutputLocation`. If not set, ask the
user for an S3 path to store query results.

## Confirmation

⏸ Confirm the discovered configuration with the user before proceeding:
- DataZone domain (or Glue-only mode)
- Athena workgroup
- Output location