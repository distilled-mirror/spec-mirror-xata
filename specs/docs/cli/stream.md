> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# stream

> Run pgstream to stream PostgreSQL data to another destination

<Note>
  The pgstream binary version is pinned by the CLI and can be overridden with the `XATA_PGSTREAM_BINARY_VERSION` environment variable.
</Note>

Every command below also takes `-h, --help`.

* [`xata stream destroy`](#destroy) — Remove the pgstream setup from a source database

## destroy

Remove the pgstream setup from a source database

Drops the replication slot along with the tables, functions and triggers pgstream created, and the pgstream schema itself.

```bash theme={null}
xata stream destroy [--config value] [--log-format value] [--log-level trace|debug|info|warn|error|fatal|panic] [--no-color] [--migrations-only] [--replication-slot value] [--slot-only] [--with-injector] (--source-url value) [--profile value] [--debug] [--json]
```

<ParamField path="--config" type="string">
  .env or .yaml config file to use with pgstream if any
</ParamField>

<ParamField path="--log-format" type="string">
  Log output format. One of console, json
</ParamField>

<ParamField path="--log-level" type="trace | debug | info | warn | error | fatal | panic" default="info">
  Log level for pgstream
</ParamField>

<ParamField path="--no-color" type="boolean">
  Disable ANSI color codes in console log output (ignored when --log-format=json)
</ParamField>

<ParamField path="--migrations-only" type="boolean">
  Whether to only revert the database migrations
</ParamField>

<ParamField path="--replication-slot" type="string">
  Name of the postgres replication slot to be deleted by pgstream from the source url
</ParamField>

<ParamField path="--slot-only" type="boolean">
  Whether to only drop the replication slot, leaving the pgstream schema and migrations in place
</ParamField>

<ParamField path="--with-injector" type="boolean">
  Whether to also destroy the injector related database objects
</ParamField>

<ParamField path="--source-url" type="string" required>
  The source PostgreSQL URL
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
