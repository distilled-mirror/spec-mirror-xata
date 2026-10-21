> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# clone

> Clone another PostgreSQL database with anonymization

Copies an external PostgreSQL database into a Xata branch, once with `start` or continuously with `stream`, and can anonymize columns on the way in. `config` writes the anonymization rules the other two read.

Every command below also takes `-h, --help`.

* [`xata clone start`](#start) — Snapshot a PostgreSQL database into a Xata branch
* [`xata clone config`](#config) — Write the anonymization rules that clone start and stream apply
* [`xata clone stream`](#stream) — Stream a PostgreSQL database into a Xata branch continuously

## start

Snapshot a PostgreSQL database into a Xata branch

Copies the source database into the branch once. The anonymization rules in `.xata/clone.yaml` are applied on the way in, and strict validation refuses to run until every table and column is covered by them.

```bash theme={null}
xata clone start [--config value] [--log-format value] [--log-level trace|debug|info|warn|error|fatal|panic] [--no-color] [--debug-profile] [--dump-file value] (--source-url value) [--organization value] [--project value] [--branch value] [--database value] [--filter-tables value] [--validation-mode strict|relaxed|prompt] [--role value] [--copy-roles] [--tune-target] [--profile value] [--debug] [--json]
```

**--config** `string`

.env or .yaml config file to use with pgstream if any

**--log-format** `string`

Log output format. One of console, json

**--log-level** `trace | debug | info | warn | error | fatal | panic` (default: info)

Log level for pgstream

**--no-color** `boolean`

Disable ANSI color codes in console log output (ignored when --log-format=json)

**--debug-profile** `boolean`

Whether to produce CPU and memory profile files, as well as exposing a /debug/pprof endpoint on localhost:6060

**--dump-file** `string`

File where the pg\_dump output will be written

**--source-url** `string` *required*

The source URL of the database to clone

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--database** `string`

Database name on the branch

**--filter-tables** `string` (default: *.*)

Tables to filter

**--validation-mode** `strict | relaxed | prompt` (default: prompt)

Anonymization validation mode, strict implies that all tables and columns should be specified

**--role** `string`

Postgres role to use for the clone

**--copy-roles** `boolean` (default: false)

Copy roles, owners, and privileges to the target

**--tune-target** `boolean` (default: false)

Temporarily tune the target branch for bulk loading, reverting the change when the clone stops. Raises max\_wal\_size on the branch and the maintenance settings on the index rebuild.

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

## config

Write the anonymization rules that clone start and stream apply

Inspects the source database and writes `.xata/clone.yaml`, interactively or with AI, so the columns that carry personal data are transformed as they are copied.

```bash theme={null}
xata clone config (--source-url value) [--mode auto|prompt|web|ai] [--validation-mode strict|relaxed|prompt] [--organization value] [--project value] [--branch value] [--prompt value] [--model gemini-2.5-flash|claude-sonnet-4-6|claude-haiku-4-5-20251001] [--profile value] [--debug] [--json]
```

**--source-url** `string` *required*

The source URL of the database to clone

**--mode** `auto | prompt | web | ai` (default: prompt)

The assisting mode to help with the configuration generation

**--validation-mode** `strict | relaxed | prompt` (default: prompt)

Anonymization validation mode, strict implies that all tables and columns should be specified

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--prompt** `string`

Instructions for AI mode (e.g., which columns to anonymize, specific transformers to use)

**--model** `gemini-2.5-flash | claude-sonnet-4-6 | claude-haiku-4-5-20251001`

AI model, gemini-2.5-flash by default

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

## stream

Stream a PostgreSQL database into a Xata branch continuously

Copies the tables `--filter-tables` selects first, then follows the source through logical replication, so the branch keeps up with it until the command is stopped.

```bash theme={null}
xata clone stream [--config value] [--log-format value] [--log-level trace|debug|info|warn|error|fatal|panic] [--no-color] [--debug-profile] [--dump-file value] (--source-url value) [--organization value] [--project value] [--branch value] [--database value] [--filter-tables value] [--validation-mode strict|relaxed|prompt] [--role value] [--copy-roles] [--skip-ddl-tracking] [--replication-slot value] [--profile value] [--debug] [--json]
```

**--config** `string`

.env or .yaml config file to use with pgstream if any

**--log-format** `string`

Log output format. One of console, json

**--log-level** `trace | debug | info | warn | error | fatal | panic` (default: info)

Log level for pgstream

**--no-color** `boolean`

Disable ANSI color codes in console log output (ignored when --log-format=json)

**--debug-profile** `boolean`

Whether to expose a /debug/pprof endpoint on localhost:6060

**--dump-file** `string`

File where the pg\_dump output will be written if initial snapshot is enabled

**--source-url** `string` *required*

The source URL of the database to stream from

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--database** `string`

Database name on the branch

**--filter-tables** `string` (default: *.*)

Tables to filter

**--validation-mode** `strict | relaxed | prompt` (default: prompt)

Anonymization validation mode, strict implies that all tables and columns should be specified

**--role** `string`

Postgres role to use for streaming (it should have at least REPLICATION privilege)

**--copy-roles** `boolean` (default: false)

Copy roles, owners, and privileges to the target

**--skip-ddl-tracking** `boolean` (default: false)

Skip DDL tracking during streaming. Useful for managed PostgreSQL services that do not support superuser access required for event triggers. Requires --replication-slot flag to be set with a pre-created replication slot on the source database.

**--replication-slot** `string`

Name of the replication slot on the source database. Overrides the default slot name

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
