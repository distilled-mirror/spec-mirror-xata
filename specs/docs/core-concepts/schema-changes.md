> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Schema Changes

> Zero-downtime schema migrations with automatic rollback support

Schema changes are a critical part of maintaining a healthy and evolving database. Whether it's adding new columns, creating indexes for faster queries, or normalizing tables to reduce data duplication, schema changes keep the database in sync with the application's needs.

However, schema changes in PostgreSQL come with significant challenges. Applying them in a production environment without downtime can be tricky. Changes to large tables, such as adding not-null unique columns or modifying data types, can lock the table for an extended period of time, essentially causing downtime.

Orchestrating application and schema changes in PostgreSQL is tricky because both need to evolve together without breaking functionality. For example, renaming a column is a simple operation from the Postgres point of view, however it is difficult to accomplish in a backwards compatible way for the application.

You can use any schema changes or schema migrations tool with Xata, however we integrate `pgroll` to solve these challenges. It is accessible directly in the CLI with the [`xata roll`](/docs/cli/roll) command.

## pgroll - Zero downtime, reversible, schema changes for PostgreSQL

[pgroll](https://github.com/xataio/pgroll) is a schema migration tool for Postgres. It is designed for application developers working on applications that require frequent schema changes but also need to maintain zero downtime around those schema changes.

There are two aspects that characterize `pgroll`'s approach to migrations.

### Multi-version migrations

Making a schema change with `pgroll` results in two versions of the schema; the one before the change and the one after the change - this allows applications to select which version of the schema they want to work with and allows side-by-side rollout of applications that require the new schema changes with old applications that may be incompatible with it.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/schema-changes-flow.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=0fef866af4697da52e5b38f83f96f8d4" alt="Schema changes flow" className="rounded-lg" width="2024" height="1320" data-path="images/schema-changes-flow.png" />

### Lock-safe migrations

Migrations using `pgroll` are expressed declaratively, rather than using SQL directly. This allows `pgroll` to implement the steps required to perform the schema change in a safe manner, ensuring that any locks required on the affected objects are held for the shortest possible time.

***

For more details about how `pgroll` works, please visit its [documentation](https://pgroll.com/docs/latest/getting-started).
