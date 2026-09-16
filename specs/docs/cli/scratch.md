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
xata scratch [--organization value] [--project value] [--parent-branch value] [--database value] [--execute value] [--profile value] [--debug] [--json] <command>...
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

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean">
  Print where each resolved value came from
</ParamField>

<ParamField path="--json" type="boolean">
  Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.
</ParamField>

<ParamField path="command" type="string">
  Binary command to run with scratch database environment variables; pass arguments to the binary after --
</ParamField>

**Examples:**

```bash theme={null}
# Run SQL with the built-in client
xata scratch --execute "select count(*) from users"
# Run SQL using the short execute flag
xata scratch -x "select count(*) from users"
# Run psql with arguments
xata scratch -- psql -c "select count(*) from users"
# Run a database tool against the scratch branch
xata scratch -- npm run migrate
```
