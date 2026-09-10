> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# roll

> Run pgroll to manage PostgreSQL schema migrations

<Note>
  The pgroll binary version is pinned by the CLI and can be overridden with the `XATA_PGROLL_BINARY_VERSION` environment variable.
</Note>

Every command below also takes `-h, --help`.

* [`xata roll baseline`](#baseline) — Create a baseline migration for an existing database schema
* [`xata roll complete`](#complete) — Complete an ongoing migration with the operations present in the given file
* [`xata roll init`](#init) — Initialize pgroll in the target database
* [`xata roll latest`](#latest) — Print the name of the latest schema version or migration
  * [`xata roll latest migration`](#latest-migration) — Print the latest migration name
  * [`xata roll latest schema`](#latest-schema) — Print the latest version schema name
* [`xata roll migrate`](#migrate) — Apply outstanding migrations from a directory to a database
* [`xata roll update`](#update) — Update outdated migrations in a directory
* [`xata roll pull`](#pull) — Pull migration history from the target database and write it to disk
* [`xata roll rollback`](#rollback) — Roll back an ongoing migration
* [`xata roll start`](#start) — Start a migration for the operations present in the given file
* [`xata roll status`](#status) — Show pgroll status
* [`xata roll convert`](#convert) — Convert SQL statements to a pgroll migration

## baseline

Create a baseline migration for an existing database schema

```bash theme={null}
xata roll baseline [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema value] [--verbose value] [--organization value] [--project value] [--branch value] [--database value] [--json value] [--yes value] [--profile value] [--debug] [<version>] [<folder>]
```

<ParamField path="--lock-timeout" type="string">
  Postgres lock timeout in milliseconds for pgroll DDL operations
</ParamField>

<ParamField path="--pgroll-schema" type="string">
  Postgres schema to use for pgroll internal state
</ParamField>

<ParamField path="--postgres-url" type="string">
  Postgres URL
</ParamField>

<ParamField path="--role" type="string">
  Optional postgres role to set when executing migrations
</ParamField>

<ParamField path="--schema" type="string">
  Postgres schema to use for the migration
</ParamField>

<ParamField path="--use-version-schema" type="string">
  Create version schemas for each migration
</ParamField>

<ParamField path="--verbose" type="string">
  Enable verbose logging
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

<ParamField path="--json" type="string">
  Output in JSON format instead of YAML
</ParamField>

<ParamField path="--yes" type="string">
  Skip confirmation prompt
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

<ParamField path="version" type="string">
  Version name for the baseline, for example 01\_initial\_schema
</ParamField>

<ParamField path="folder" type="string">
  The directory that contains the migrations
</ParamField>

## complete

Complete an ongoing migration with the operations present in the given file

```bash theme={null}
xata roll complete [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema value] [--verbose value] [--organization value] [--project value] [--branch value] [--database value] [--profile value] [--debug]
```

<ParamField path="--lock-timeout" type="string">
  Postgres lock timeout in milliseconds for pgroll DDL operations
</ParamField>

<ParamField path="--pgroll-schema" type="string">
  Postgres schema to use for pgroll internal state
</ParamField>

<ParamField path="--postgres-url" type="string">
  Postgres URL
</ParamField>

<ParamField path="--role" type="string">
  Optional postgres role to set when executing migrations
</ParamField>

<ParamField path="--schema" type="string">
  Postgres schema to use for the migration
</ParamField>

<ParamField path="--use-version-schema" type="string">
  Create version schemas for each migration
</ParamField>

<ParamField path="--verbose" type="string">
  Enable verbose logging
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

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

## init

Initialize pgroll in the target database

```bash theme={null}
xata roll init [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema value] [--verbose value] [--organization value] [--project value] [--branch value] [--database value] [--profile value] [--debug]
```

<ParamField path="--lock-timeout" type="string">
  Postgres lock timeout in milliseconds for pgroll DDL operations
</ParamField>

<ParamField path="--pgroll-schema" type="string">
  Postgres schema to use for pgroll internal state
</ParamField>

<ParamField path="--postgres-url" type="string">
  Postgres URL
</ParamField>

<ParamField path="--role" type="string">
  Optional postgres role to set when executing migrations
</ParamField>

<ParamField path="--schema" type="string">
  Postgres schema to use for the migration
</ParamField>

<ParamField path="--use-version-schema" type="string">
  Create version schemas for each migration
</ParamField>

<ParamField path="--verbose" type="string">
  Enable verbose logging
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

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

## latest

Print the name of the latest schema version or migration

### latest migration

Print the latest migration name

```bash theme={null}
xata roll latest migration [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema value] [--verbose value] [--organization value] [--project value] [--branch value] [--database value] [--local value] [--profile value] [--debug]
```

<ParamField path="--lock-timeout" type="string">
  Postgres lock timeout in milliseconds for pgroll DDL operations
</ParamField>

<ParamField path="--pgroll-schema" type="string">
  Postgres schema to use for pgroll internal state
</ParamField>

<ParamField path="--postgres-url" type="string">
  Postgres URL
</ParamField>

<ParamField path="--role" type="string">
  Optional postgres role to set when executing migrations
</ParamField>

<ParamField path="--schema" type="string">
  Postgres schema to use for the migration
</ParamField>

<ParamField path="--use-version-schema" type="string">
  Create version schemas for each migration
</ParamField>

<ParamField path="--verbose" type="string">
  Enable verbose logging
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

<ParamField path="--local" type="string">
  Retrieve the latest migration from a local migration directory
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

### latest schema

Print the latest version schema name

```bash theme={null}
xata roll latest schema [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema value] [--verbose value] [--organization value] [--project value] [--branch value] [--database value] [--local value] [--profile value] [--debug]
```

<ParamField path="--lock-timeout" type="string">
  Postgres lock timeout in milliseconds for pgroll DDL operations
</ParamField>

<ParamField path="--pgroll-schema" type="string">
  Postgres schema to use for pgroll internal state
</ParamField>

<ParamField path="--postgres-url" type="string">
  Postgres URL
</ParamField>

<ParamField path="--role" type="string">
  Optional postgres role to set when executing migrations
</ParamField>

<ParamField path="--schema" type="string">
  Postgres schema to use for the migration
</ParamField>

<ParamField path="--use-version-schema" type="string">
  Create version schemas for each migration
</ParamField>

<ParamField path="--verbose" type="string">
  Enable verbose logging
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

<ParamField path="--local" type="string">
  Retrieve the latest version from a local migration directory
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

## migrate

Apply outstanding migrations from a directory to a database

```bash theme={null}
xata roll migrate [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema value] [--verbose value] [--organization value] [--project value] [--branch value] [--database value] [--backfill-batch-delay value] [--backfill-batch-size value] [--complete value] [--expect-one value] [--profile value] [--debug] [<folder>]
```

<ParamField path="--lock-timeout" type="string">
  Postgres lock timeout in milliseconds for pgroll DDL operations
</ParamField>

<ParamField path="--pgroll-schema" type="string">
  Postgres schema to use for pgroll internal state
</ParamField>

<ParamField path="--postgres-url" type="string">
  Postgres URL
</ParamField>

<ParamField path="--role" type="string">
  Optional postgres role to set when executing migrations
</ParamField>

<ParamField path="--schema" type="string">
  Postgres schema to use for the migration
</ParamField>

<ParamField path="--use-version-schema" type="string">
  Create version schemas for each migration
</ParamField>

<ParamField path="--verbose" type="string">
  Enable verbose logging
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

<ParamField path="--backfill-batch-delay" type="string">
  Duration of delay between batch backfills (eg. 1s, 1000ms)
</ParamField>

<ParamField path="--backfill-batch-size" type="string">
  Number of rows backfilled in each batch
</ParamField>

<ParamField path="--complete" type="string">
  Complete the final migration rather than leaving it active
</ParamField>

<ParamField path="--expect-one" type="string">
  Abort if there is more than one migration to be applied
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

<ParamField path="folder" type="string">
  The directory that contains the migrations
</ParamField>

## update

Update outdated migrations in a directory

```bash theme={null}
xata roll update [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema value] [--verbose value] [--organization value] [--project value] [--branch value] [--database value] [--json value] [--profile value] [--debug] [<folder>]
```

<ParamField path="--lock-timeout" type="string">
  Postgres lock timeout in milliseconds for pgroll DDL operations
</ParamField>

<ParamField path="--pgroll-schema" type="string">
  Postgres schema to use for pgroll internal state
</ParamField>

<ParamField path="--postgres-url" type="string">
  Postgres URL
</ParamField>

<ParamField path="--role" type="string">
  Optional postgres role to set when executing migrations
</ParamField>

<ParamField path="--schema" type="string">
  Postgres schema to use for the migration
</ParamField>

<ParamField path="--use-version-schema" type="string">
  Create version schemas for each migration
</ParamField>

<ParamField path="--verbose" type="string">
  Enable verbose logging
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

<ParamField path="--json" type="string">
  Output migration file in JSON format instead of YAML
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

<ParamField path="folder" type="string">
  The directory that contains the migrations
</ParamField>

## pull

Pull migration history from the target database and write it to disk

```bash theme={null}
xata roll pull [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema value] [--verbose value] [--organization value] [--project value] [--branch value] [--database value] [--json value] [--profile value] [--debug] [<folder>]
```

<ParamField path="--lock-timeout" type="string">
  Postgres lock timeout in milliseconds for pgroll DDL operations
</ParamField>

<ParamField path="--pgroll-schema" type="string">
  Postgres schema to use for pgroll internal state
</ParamField>

<ParamField path="--postgres-url" type="string">
  Postgres URL
</ParamField>

<ParamField path="--role" type="string">
  Optional postgres role to set when executing migrations
</ParamField>

<ParamField path="--schema" type="string">
  Postgres schema to use for the migration
</ParamField>

<ParamField path="--use-version-schema" type="string">
  Create version schemas for each migration
</ParamField>

<ParamField path="--verbose" type="string">
  Enable verbose logging
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

<ParamField path="--json" type="string">
  Output each migration in JSON format instead of YAML
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

<ParamField path="folder" type="string">
  The target directory to pull migrations into
</ParamField>

## rollback

Roll back an ongoing migration

```bash theme={null}
xata roll rollback [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema value] [--verbose value] [--organization value] [--project value] [--branch value] [--database value] [--profile value] [--debug]
```

<ParamField path="--lock-timeout" type="string">
  Postgres lock timeout in milliseconds for pgroll DDL operations
</ParamField>

<ParamField path="--pgroll-schema" type="string">
  Postgres schema to use for pgroll internal state
</ParamField>

<ParamField path="--postgres-url" type="string">
  Postgres URL
</ParamField>

<ParamField path="--role" type="string">
  Optional postgres role to set when executing migrations
</ParamField>

<ParamField path="--schema" type="string">
  Postgres schema to use for the migration
</ParamField>

<ParamField path="--use-version-schema" type="string">
  Create version schemas for each migration
</ParamField>

<ParamField path="--verbose" type="string">
  Enable verbose logging
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

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

## start

Start a migration for the operations present in the given file

```bash theme={null}
xata roll start [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema value] [--verbose value] [--organization value] [--project value] [--branch value] [--database value] [--backfill-batch-delay value] [--backfill-batch-size value] [--complete value] [--skip-validation value] [--profile value] [--debug] <file>
```

<ParamField path="--lock-timeout" type="string">
  Postgres lock timeout in milliseconds for pgroll DDL operations
</ParamField>

<ParamField path="--pgroll-schema" type="string">
  Postgres schema to use for pgroll internal state
</ParamField>

<ParamField path="--postgres-url" type="string">
  Postgres URL
</ParamField>

<ParamField path="--role" type="string">
  Optional postgres role to set when executing migrations
</ParamField>

<ParamField path="--schema" type="string">
  Postgres schema to use for the migration
</ParamField>

<ParamField path="--use-version-schema" type="string">
  Create version schemas for each migration
</ParamField>

<ParamField path="--verbose" type="string">
  Enable verbose logging
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

<ParamField path="--backfill-batch-delay" type="string">
  Duration of delay between batch backfills (eg. 1s, 1000ms)
</ParamField>

<ParamField path="--backfill-batch-size" type="string">
  Number of rows backfilled in each batch
</ParamField>

<ParamField path="--complete" type="string">
  Mark the migration as complete
</ParamField>

<ParamField path="--skip-validation" type="string">
  Skip migration validation
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

<ParamField path="file" type="string" required>
  The migration file to start
</ParamField>

## status

Show pgroll status

```bash theme={null}
xata roll status [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema value] [--verbose value] [--organization value] [--project value] [--branch value] [--database value] [--profile value] [--debug]
```

<ParamField path="--lock-timeout" type="string">
  Postgres lock timeout in milliseconds for pgroll DDL operations
</ParamField>

<ParamField path="--pgroll-schema" type="string">
  Postgres schema to use for pgroll internal state
</ParamField>

<ParamField path="--postgres-url" type="string">
  Postgres URL
</ParamField>

<ParamField path="--role" type="string">
  Optional postgres role to set when executing migrations
</ParamField>

<ParamField path="--schema" type="string">
  Postgres schema to use for the migration
</ParamField>

<ParamField path="--use-version-schema" type="string">
  Create version schemas for each migration
</ParamField>

<ParamField path="--verbose" type="string">
  Enable verbose logging
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

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

## convert

Convert SQL statements to a pgroll migration

```bash theme={null}
xata roll convert [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema value] [--verbose value] [--organization value] [--project value] [--branch value] [--database value] [--json value] [--profile value] [--debug] <file>
```

<ParamField path="--lock-timeout" type="string">
  Postgres lock timeout in milliseconds for pgroll DDL operations
</ParamField>

<ParamField path="--pgroll-schema" type="string">
  Postgres schema to use for pgroll internal state
</ParamField>

<ParamField path="--postgres-url" type="string">
  Postgres URL
</ParamField>

<ParamField path="--role" type="string">
  Optional postgres role to set when executing migrations
</ParamField>

<ParamField path="--schema" type="string">
  Postgres schema to use for the migration
</ParamField>

<ParamField path="--use-version-schema" type="string">
  Create version schemas for each migration
</ParamField>

<ParamField path="--verbose" type="string">
  Enable verbose logging
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

<ParamField path="--json" type="string">
  Output migration file in JSON format instead of YAML
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

<ParamField path="file" type="string" required>
  The migration file to convert
</ParamField>
