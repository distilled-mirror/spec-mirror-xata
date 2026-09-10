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
xata clone start [--config value] [--log-format value] [--log-level trace|debug|info|warn|error|fatal|panic] [--no-color value] [--dump-file value] [--postgres-url value] [--profile value] [--reset value] [--tables value] [--target value] [--target-url value] (--source-url value) [--organization value] [--project value] [--branch value] [--database value] [--filter-tables value] [--validation-mode strict|relaxed|prompt] [--role value] [--copy-roles] [--tune-target] [--debug]
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

<ParamField path="--dump-file" type="string">
  File where the pg\_dump output will be written
</ParamField>

<ParamField path="--postgres-url" type="string">
  Source postgres database to perform the snapshot from
</ParamField>

<ParamField path="--profile" type="string">
  Whether to produce CPU and memory profile files, as well as exposing a /debug/pprof endpoint on localhost:6060
</ParamField>

<ParamField path="--reset" type="string">
  Whether to reset the target before snapshotting (only for postgres target). Destructive: drops the target objects before the data is copied, so a failed snapshot leaves the target without its previous contents
</ParamField>

<ParamField path="--tables" type="string">
  List of tables to snapshot, in the format `<schema>.<table>`. If not specified, the schema `public` will be assumed. Wildcards are supported
</ParamField>

<ParamField path="--target" type="string">
  Target type. One of postgres, opensearch, elasticsearch, kafka
</ParamField>

<ParamField path="--target-url" type="string">
  Target URL
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

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

## config

Write the anonymization rules that clone start and stream apply

Inspects the source database and writes `.xata/clone.yaml`, interactively or with AI, so the columns that carry personal data are transformed as they are copied.

```bash theme={null}
xata clone config (--source-url value) [--mode auto|prompt|web|ai] [--validation-mode strict|relaxed|prompt] [--organization value] [--project value] [--branch value] [--prompt value] [--model value] [--profile value] [--debug]
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

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

## stream

Stream a PostgreSQL database into a Xata branch continuously

Follows the source through logical replication, so the branch keeps up with it until the command is stopped. Passing `--snapshot-tables` copies those tables first, otherwise only the changes from now on are streamed.

```bash theme={null}
xata clone stream [--config value] [--log-format value] [--log-level trace|debug|info|warn|error|fatal|panic] [--no-color value] [--data-only value] [--dump-file value] [--init value] [--profile value] [--replication-slot value] [--reset value] [--snapshot-tables value] [--source value] (--source-url value) [--target value] [--target-url value] [--upgrade value] [--with-injector value] [--organization value] [--project value] [--branch value] [--database value] [--filter-tables value] [--validation-mode strict|relaxed|prompt] [--role value] [--copy-roles] [--skip-ddl-tracking] [--debug]
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

<ParamField path="--data-only" type="string">
  When used with --snapshot-tables, skip schema restore and only snapshot data (use when schema is already present on target)
</ParamField>

<ParamField path="--dump-file" type="string">
  File where the pg\_dump output will be written if initial snapshot is enabled
</ParamField>

<ParamField path="--init" type="string">
  Whether to initialize pgstream before starting replication
</ParamField>

<ParamField path="--profile" type="string">
  Whether to expose a /debug/pprof endpoint on localhost:6060
</ParamField>

<ParamField path="--replication-slot" type="string">
  Name of the replication slot on the source database. Overrides the default slot name
</ParamField>

<ParamField path="--reset" type="string">
  Whether to reset the target before snapshotting (only for postgres target). Destructive: drops the target objects before the data is copied, so a failed snapshot leaves the target without its previous contents
</ParamField>

<ParamField path="--snapshot-tables" type="string">
  List of tables to snapshot if initial snapshot is required, in the format `<schema>.<table>`. If not specified, the schema `public` will be assumed. Wildcards are supported
</ParamField>

<ParamField path="--source" type="string">
  Source type. One of postgres, kafka
</ParamField>

<ParamField path="--source-url" type="string" required>
  The source URL of the database to stream from
</ParamField>

<ParamField path="--target" type="string">
  Target type. One of postgres, opensearch, elasticsearch, kafka
</ParamField>

<ParamField path="--target-url" type="string">
  Target URL
</ParamField>

<ParamField path="--upgrade" type="string">
  Clean up v0.9.x state before initializing (idempotent, safe for repeated use; implies --init)
</ParamField>

<ParamField path="--with-injector" type="string">
  Whether to enable the injection of pgstream metadata to the WAL events. Required for search targets.
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

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>
