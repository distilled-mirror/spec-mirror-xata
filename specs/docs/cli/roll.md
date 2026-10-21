> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# roll

> Run pgroll to manage PostgreSQL schema migrations

> [!NOTE]
>
> The pgroll binary version is pinned by the CLI and can be overridden with the `XATA_PGROLL_BINARY_VERSION` environment variable.

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
xata roll baseline [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema] [--verbose] [--organization value] [--project value] [--branch value] [--database value] [--json] [--yes] [--profile value] [--debug] [<version>] [<folder>]
```

**--lock-timeout** `string`

Postgres lock timeout in milliseconds for pgroll DDL operations

**--pgroll-schema** `string`

Postgres schema to use for pgroll internal state

**--postgres-url** `string`

Postgres URL

**--role** `string`

Optional postgres role to set when executing migrations

**--schema** `string`

Postgres schema to use for the migration

**--use-version-schema** `boolean`

Create version schemas for each migration

**--verbose** `boolean`

Enable verbose logging

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--database** `string`

Database name on the branch

**--json** `boolean`

Output in JSON format instead of YAML

**--yes** `boolean`

Skip confirmation prompt

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**version** `string`

Version name for the baseline, for example 01\_initial\_schema

**folder** `string`

The directory that contains the migrations

## complete

Complete an ongoing migration with the operations present in the given file

```bash theme={null}
xata roll complete [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema] [--verbose] [--organization value] [--project value] [--branch value] [--database value] [--profile value] [--debug] [--json]
```

**--lock-timeout** `string`

Postgres lock timeout in milliseconds for pgroll DDL operations

**--pgroll-schema** `string`

Postgres schema to use for pgroll internal state

**--postgres-url** `string`

Postgres URL

**--role** `string`

Optional postgres role to set when executing migrations

**--schema** `string`

Postgres schema to use for the migration

**--use-version-schema** `boolean`

Create version schemas for each migration

**--verbose** `boolean`

Enable verbose logging

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--database** `string`

Database name on the branch

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

## init

Initialize pgroll in the target database

```bash theme={null}
xata roll init [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema] [--verbose] [--organization value] [--project value] [--branch value] [--database value] [--profile value] [--debug] [--json]
```

**--lock-timeout** `string`

Postgres lock timeout in milliseconds for pgroll DDL operations

**--pgroll-schema** `string`

Postgres schema to use for pgroll internal state

**--postgres-url** `string`

Postgres URL

**--role** `string`

Optional postgres role to set when executing migrations

**--schema** `string`

Postgres schema to use for the migration

**--use-version-schema** `boolean`

Create version schemas for each migration

**--verbose** `boolean`

Enable verbose logging

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--database** `string`

Database name on the branch

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

## latest

Print the name of the latest schema version or migration

### latest migration

Print the latest migration name

```bash theme={null}
xata roll latest migration [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema] [--verbose] [--organization value] [--project value] [--branch value] [--database value] [--local value] [--profile value] [--debug] [--json]
```

**--lock-timeout** `string`

Postgres lock timeout in milliseconds for pgroll DDL operations

**--pgroll-schema** `string`

Postgres schema to use for pgroll internal state

**--postgres-url** `string`

Postgres URL

**--role** `string`

Optional postgres role to set when executing migrations

**--schema** `string`

Postgres schema to use for the migration

**--use-version-schema** `boolean`

Create version schemas for each migration

**--verbose** `boolean`

Enable verbose logging

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--database** `string`

Database name on the branch

**--local** `string`

Retrieve the latest migration from a local migration directory

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

### latest schema

Print the latest version schema name

```bash theme={null}
xata roll latest schema [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema] [--verbose] [--organization value] [--project value] [--branch value] [--database value] [--local value] [--profile value] [--debug] [--json]
```

**--lock-timeout** `string`

Postgres lock timeout in milliseconds for pgroll DDL operations

**--pgroll-schema** `string`

Postgres schema to use for pgroll internal state

**--postgres-url** `string`

Postgres URL

**--role** `string`

Optional postgres role to set when executing migrations

**--schema** `string`

Postgres schema to use for the migration

**--use-version-schema** `boolean`

Create version schemas for each migration

**--verbose** `boolean`

Enable verbose logging

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--database** `string`

Database name on the branch

**--local** `string`

Retrieve the latest version from a local migration directory

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

## migrate

Apply outstanding migrations from a directory to a database

```bash theme={null}
xata roll migrate [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema] [--verbose] [--organization value] [--project value] [--branch value] [--database value] [--backfill-batch-delay value] [--backfill-batch-size value] [--complete] [--expect-one] [--profile value] [--debug] [--json] [<folder>]
```

**--lock-timeout** `string`

Postgres lock timeout in milliseconds for pgroll DDL operations

**--pgroll-schema** `string`

Postgres schema to use for pgroll internal state

**--postgres-url** `string`

Postgres URL

**--role** `string`

Optional postgres role to set when executing migrations

**--schema** `string`

Postgres schema to use for the migration

**--use-version-schema** `boolean`

Create version schemas for each migration

**--verbose** `boolean`

Enable verbose logging

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--database** `string`

Database name on the branch

**--backfill-batch-delay** `string`

Duration of delay between batch backfills (eg. 1s, 1000ms)

**--backfill-batch-size** `string`

Number of rows backfilled in each batch

**--complete** `boolean`

Complete the final migration rather than leaving it active

**--expect-one** `boolean`

Abort if there is more than one migration to be applied

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**folder** `string`

The directory that contains the migrations

## update

Update outdated migrations in a directory

```bash theme={null}
xata roll update [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema] [--verbose] [--organization value] [--project value] [--branch value] [--database value] [--json] [--profile value] [--debug] [<folder>]
```

**--lock-timeout** `string`

Postgres lock timeout in milliseconds for pgroll DDL operations

**--pgroll-schema** `string`

Postgres schema to use for pgroll internal state

**--postgres-url** `string`

Postgres URL

**--role** `string`

Optional postgres role to set when executing migrations

**--schema** `string`

Postgres schema to use for the migration

**--use-version-schema** `boolean`

Create version schemas for each migration

**--verbose** `boolean`

Enable verbose logging

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--database** `string`

Database name on the branch

**--json** `boolean`

Output migration file in JSON format instead of YAML

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**folder** `string`

The directory that contains the migrations

## pull

Pull migration history from the target database and write it to disk

```bash theme={null}
xata roll pull [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema] [--verbose] [--organization value] [--project value] [--branch value] [--database value] [--json] [--profile value] [--debug] [<folder>]
```

**--lock-timeout** `string`

Postgres lock timeout in milliseconds for pgroll DDL operations

**--pgroll-schema** `string`

Postgres schema to use for pgroll internal state

**--postgres-url** `string`

Postgres URL

**--role** `string`

Optional postgres role to set when executing migrations

**--schema** `string`

Postgres schema to use for the migration

**--use-version-schema** `boolean`

Create version schemas for each migration

**--verbose** `boolean`

Enable verbose logging

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--database** `string`

Database name on the branch

**--json** `boolean`

Output each migration in JSON format instead of YAML

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**folder** `string`

The target directory to pull migrations into

## rollback

Roll back an ongoing migration

```bash theme={null}
xata roll rollback [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema] [--verbose] [--organization value] [--project value] [--branch value] [--database value] [--profile value] [--debug] [--json]
```

**--lock-timeout** `string`

Postgres lock timeout in milliseconds for pgroll DDL operations

**--pgroll-schema** `string`

Postgres schema to use for pgroll internal state

**--postgres-url** `string`

Postgres URL

**--role** `string`

Optional postgres role to set when executing migrations

**--schema** `string`

Postgres schema to use for the migration

**--use-version-schema** `boolean`

Create version schemas for each migration

**--verbose** `boolean`

Enable verbose logging

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--database** `string`

Database name on the branch

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

## start

Start a migration for the operations present in the given file

```bash theme={null}
xata roll start [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema] [--verbose] [--organization value] [--project value] [--branch value] [--database value] [--backfill-batch-delay value] [--backfill-batch-size value] [--complete] [--skip-validation] [--profile value] [--debug] [--json] <file>
```

**--lock-timeout** `string`

Postgres lock timeout in milliseconds for pgroll DDL operations

**--pgroll-schema** `string`

Postgres schema to use for pgroll internal state

**--postgres-url** `string`

Postgres URL

**--role** `string`

Optional postgres role to set when executing migrations

**--schema** `string`

Postgres schema to use for the migration

**--use-version-schema** `boolean`

Create version schemas for each migration

**--verbose** `boolean`

Enable verbose logging

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--database** `string`

Database name on the branch

**--backfill-batch-delay** `string`

Duration of delay between batch backfills (eg. 1s, 1000ms)

**--backfill-batch-size** `string`

Number of rows backfilled in each batch

**--complete** `boolean`

Mark the migration as complete

**--skip-validation** `boolean`

Skip migration validation

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**file** `string` *required*

The migration file to start

## status

Show pgroll status

```bash theme={null}
xata roll status [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema] [--verbose] [--organization value] [--project value] [--branch value] [--database value] [--profile value] [--debug] [--json]
```

**--lock-timeout** `string`

Postgres lock timeout in milliseconds for pgroll DDL operations

**--pgroll-schema** `string`

Postgres schema to use for pgroll internal state

**--postgres-url** `string`

Postgres URL

**--role** `string`

Optional postgres role to set when executing migrations

**--schema** `string`

Postgres schema to use for the migration

**--use-version-schema** `boolean`

Create version schemas for each migration

**--verbose** `boolean`

Enable verbose logging

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--database** `string`

Database name on the branch

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

## convert

Convert SQL statements to a pgroll migration

```bash theme={null}
xata roll convert [--lock-timeout value] [--pgroll-schema value] [--postgres-url value] [--role value] [--schema value] [--use-version-schema] [--verbose] [--organization value] [--project value] [--branch value] [--database value] [--json] [--profile value] [--debug] <file>
```

**--lock-timeout** `string`

Postgres lock timeout in milliseconds for pgroll DDL operations

**--pgroll-schema** `string`

Postgres schema to use for pgroll internal state

**--postgres-url** `string`

Postgres URL

**--role** `string`

Optional postgres role to set when executing migrations

**--schema** `string`

Postgres schema to use for the migration

**--use-version-schema** `boolean`

Create version schemas for each migration

**--verbose** `boolean`

Enable verbose logging

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--database** `string`

Database name on the branch

**--json** `boolean`

Output migration file in JSON format instead of YAML

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**file** `string` *required*

The migration file to convert


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
