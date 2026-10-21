> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# stream

> Run pgstream to stream PostgreSQL data to another destination

> [!NOTE]
>
> The pgstream binary version is pinned by the CLI and can be overridden with the `XATA_PGSTREAM_BINARY_VERSION` environment variable.

Every command below also takes `-h, --help`.

* [`xata stream destroy`](#destroy) — Remove the pgstream setup from a source database

## destroy

Remove the pgstream setup from a source database

Drops the replication slot along with the tables, functions and triggers pgstream created, and the pgstream schema itself.

```bash theme={null}
xata stream destroy [--config value] [--log-format value] [--log-level trace|debug|info|warn|error|fatal|panic] [--no-color] [--migrations-only] [--replication-slot value] [--slot-only] [--with-injector] (--source-url value) [--profile value] [--debug] [--json]
```

**--config** `string`

.env or .yaml config file to use with pgstream if any

**--log-format** `string`

Log output format. One of console, json

**--log-level** `trace | debug | info | warn | error | fatal | panic` (default: info)

Log level for pgstream

**--no-color** `boolean`

Disable ANSI color codes in console log output (ignored when --log-format=json)

**--migrations-only** `boolean`

Whether to only revert the database migrations

**--replication-slot** `string`

Name of the postgres replication slot to be deleted by pgstream from the source url

**--slot-only** `boolean`

Whether to only drop the replication slot, leaving the pgstream schema and migrations in place

**--with-injector** `boolean`

Whether to also destroy the injector related database objects

**--source-url** `string` *required*

The source PostgreSQL URL

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
