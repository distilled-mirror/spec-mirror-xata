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
xata stream destroy [--config value] [--log-format value] [--log-level trace|debug|info|warn|error|fatal|panic] [--no-color value] [--migrations-only value] [--postgres-url value] [--replication-slot value] [--slot-only value] [--with-injector value] (--source-url value) [--profile value] [--debug]
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

<ParamField path="--no-color" type="string">
  Disable ANSI color codes in console log output (ignored when --log-format=json)
</ParamField>

<ParamField path="--migrations-only" type="string">
  Whether to only revert the database migrations
</ParamField>

<ParamField path="--postgres-url" type="string">
  Source postgres URL where pgstream destroy will be run
</ParamField>

<ParamField path="--replication-slot" type="string">
  Name of the postgres replication slot to be deleted by pgstream from the source url
</ParamField>

<ParamField path="--slot-only" type="string">
  Whether to only drop the replication slot, leaving the pgstream schema and migrations in place
</ParamField>

<ParamField path="--with-injector" type="string">
  Whether to also destroy the injector related database objects
</ParamField>

<ParamField path="--source-url" type="string" required>
  The source PostgreSQL URL
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>
