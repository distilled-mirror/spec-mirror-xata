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

<ParamField path="--debug-profile" type="boolean">
  Whether to produce CPU and memory profile files, as well as exposing a /debug/pprof endpoint on localhost:6060
</ParamField>

<ParamField path="--dump-file" type="string">
  File where the pg\_dump output will be written
</ParamField>

<ParamField path="--source-url" type="string" required>
  The source URL of the database to clone
</ParamField>

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--project" type="string">
  Project ID
</ParamField>

<ParamField path="--branch" type="string">
  Branch ID or name
</ParamField>

<ParamField path="--database" type="string">
  Database name on the branch
</ParamField>

<ParamField path="--filter-tables" type="string" default="*.*">
  Tables to filter
</ParamField>

<ParamField path="--validation-mode" type="strict | relaxed | prompt" default="prompt">
  Anonymization validation mode, strict implies that all tables and columns should be specified
</ParamField>

<ParamField path="--role" type="string">
  Postgres role to use for the clone
</ParamField>

<ParamField path="--copy-roles" type="boolean" default="false">
  Copy roles, owners, and privileges to the target
</ParamField>

<ParamField path="--tune-target" type="boolean" default="false">
  Temporarily tune the target branch for bulk loading, reverting the change when the clone stops. Raises max\_wal\_size on the branch and the maintenance settings on the index rebuild.
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

## config

Write the anonymization rules that clone start and stream apply

Inspects the source database and writes `.xata/clone.yaml`, interactively or with AI, so the columns that carry personal data are transformed as they are copied.

```bash theme={null}
xata clone config (--source-url value) [--mode auto|prompt|web|ai] [--validation-mode strict|relaxed|prompt] [--organization value] [--project value] [--branch value] [--prompt value] [--model value] [--profile value] [--debug] [--json]
```

<ParamField path="--source-url" type="string" required>
  The source URL of the database to clone
</ParamField>

<ParamField path="--mode" type="auto | prompt | web | ai" default="prompt">
  The assisting mode to help with the configuration generation
</ParamField>

<ParamField path="--validation-mode" type="strict | relaxed | prompt" default="prompt">
  Anonymization validation mode, strict implies that all tables and columns should be specified
</ParamField>

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--project" type="string">
  Project ID
</ParamField>

<ParamField path="--branch" type="string">
  Branch ID or name
</ParamField>

<ParamField path="--prompt" type="string">
  Instructions for AI mode (e.g., which columns to anonymize, specific transformers to use)
</ParamField>

<ParamField path="--model" type="string">
  Anthropic model override for AI mode
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

## stream

Stream a PostgreSQL database into a Xata branch continuously

Copies the tables `--filter-tables` selects first, then follows the source through logical replication, so the branch keeps up with it until the command is stopped.

```bash theme={null}
xata clone stream [--config value] [--log-format value] [--log-level trace|debug|info|warn|error|fatal|panic] [--no-color] [--debug-profile] [--dump-file value] (--source-url value) [--organization value] [--project value] [--branch value] [--database value] [--filter-tables value] [--validation-mode strict|relaxed|prompt] [--role value] [--copy-roles] [--skip-ddl-tracking] [--replication-slot value] [--profile value] [--debug] [--json]
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

<ParamField path="--debug-profile" type="boolean">
  Whether to expose a /debug/pprof endpoint on localhost:6060
</ParamField>

<ParamField path="--dump-file" type="string">
  File where the pg\_dump output will be written if initial snapshot is enabled
</ParamField>

<ParamField path="--source-url" type="string" required>
  The source URL of the database to stream from
</ParamField>

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--project" type="string">
  Project ID
</ParamField>

<ParamField path="--branch" type="string">
  Branch ID or name
</ParamField>

<ParamField path="--database" type="string">
  Database name on the branch
</ParamField>

<ParamField path="--filter-tables" type="string" default="*.*">
  Tables to filter
</ParamField>

<ParamField path="--validation-mode" type="strict | relaxed | prompt" default="prompt">
  Anonymization validation mode, strict implies that all tables and columns should be specified
</ParamField>

<ParamField path="--role" type="string">
  Postgres role to use for streaming (it should have at least REPLICATION privilege)
</ParamField>

<ParamField path="--copy-roles" type="boolean" default="false">
  Copy roles, owners, and privileges to the target
</ParamField>

<ParamField path="--skip-ddl-tracking" type="boolean" default="false">
  Skip DDL tracking during streaming. Useful for managed PostgreSQL services that do not support superuser access required for event triggers. Requires --replication-slot flag to be set with a pre-created replication slot on the source database.
</ParamField>

<ParamField path="--replication-slot" type="string">
  Name of the replication slot on the source database. Overrides the default slot name
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
