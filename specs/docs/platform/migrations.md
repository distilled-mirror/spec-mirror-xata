> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Migrations

> Zero-downtime schema migrations with automatic rollback support

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/platform/migrations.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=17b84c63929ab3f378828506c211af09" alt="Migrations interface showing version history, recent activity, and database migration management" className="rounded-lg" width="2870" height="1550" data-path="images/platform/migrations.png" />

Apply schema changes safely with zero-downtime migrations using `xata roll`. Xata provides comprehensive migration management with automatic rollback support, multi-version schema handling, and lock-safe operations. Track recent migration activity in the console and manage multiple schemas simultaneously. Learn more about [how schema changes work](/docs/core-concepts/schema-changes).

## Making Schema Changes

Xata uses [pgroll](https://github.com/xataio/pgroll) behind the scenes to enable zero-downtime, reversible schema changes for PostgreSQL. This approach allows you to apply schema changes without locking tables for extended periods, ensuring your application remains available during migrations.

### Key Features

* **Zero-downtime migrations**: Apply schema changes without service interruption
* **Automatic rollback**: Instantly revert changes if issues arise
* **Multi-version support**: Serve both old and new schema versions simultaneously
* **Lock-safe operations**: Minimal table locking during migration execution
* **Declarative approach**: Express migrations declaratively rather than using raw SQL

### Migration Process

1. **Start migration**: Begin the migration process with `xata roll migrate`
2. **Monitor progress**: Check migration status and activity in the console
3. **Test changes**: Verify application compatibility with new schema
4. **Complete migration**: Finalize changes when ready with `xata roll complete`
5. **Rollback if needed**: Use `xata roll rollback` to revert changes instantly

For detailed step-by-step instructions, see our [schema change tutorial](/docs/tutorials/schema-change).

## From the CLI

Use the Xata CLI to manage migrations programmatically:

```bash theme={null}
# Initialize migration tracking
xata roll init

# Apply migrations
xata roll migrate

# Check migration status
xata roll status

# Rollback if needed
xata roll rollback

# Complete migration
xata roll complete
```

For more CLI commands, see the [CLI Reference](/docs/cli/roll).
