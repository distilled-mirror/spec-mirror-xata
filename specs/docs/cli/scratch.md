> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# scratch

> Run SQL or a Postgres client command against a temporary scratch branch

Creates a branch from the parent given, runs what it is asked to, and deletes the branch afterwards, so a query or a migration can be tried against real data without touching an existing branch.

This command also takes `-h, --help`.

## scratch

Run SQL or a Postgres client command against a temporary scratch branch

Creates a branch from the parent given, runs what it is asked to, and deletes the branch afterwards, so a query or a migration can be tried against real data without touching an existing branch.

```bash theme={null}
xata scratch [--organization value] [--project value] [--parent-branch value] [--database value] [--execute value] [--json] [--profile value] [--debug] <command>...
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--project" type="string">
  Project ID
</ParamField>

<ParamField path="--parent-branch" type="string">
  Source branch ID or name for the scratch branch
</ParamField>

<ParamField path="--database" type="string">
  Database name
</ParamField>

<ParamField path="-x, --execute" type="string">
  SQL query to execute in the scratch branch
</ParamField>

<ParamField path="--json" type="boolean" default="false">
  Output SQL query results in JSON format
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

<ParamField path="command" type="string">
  Binary command to run with scratch database environment variables
</ParamField>

**Examples:**

```bash theme={null}
# Run a query against a throwaway copy
xata scratch --execute "select count(*) from users"
# Open a Postgres client on the scratch branch
xata scratch psql
```
