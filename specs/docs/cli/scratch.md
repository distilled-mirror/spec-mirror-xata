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

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--parent-branch** `string`

Source branch ID or name for the scratch branch

**--database** `string`

Database name

**-x, --execute** `string`

SQL query to execute in the scratch branch

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**command** `string`

Binary command to run with scratch database environment variables; pass arguments to the binary after --

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


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
