> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Create Streaming Replica

> Use Xata's streaming replication to keep your database continuously synchronized with real-time changes.

This guide shows you how to set up continuous logical streaming replication from your production PostgreSQL database to Xata, enabling real-time data synchronization with optional anonymization.

## 1. Prerequisites

* A Xata account ([sign up here](https://console.xata.io))
* The [Xata CLI](/docs/cli) installed:
  ```bash theme={null}
  curl -fsSL https://xata.io/install.sh | bash
  ```
* A PostgreSQL database with:
  * Logical replication enabled
  * Role with permissions to create a replication slow (`xata clone stream` command does that automatically)
  * Network connectivity from Xata to your database

## 2. Enable logical replication on source database

First, ensure your source PostgreSQL database has logical replication enabled. You'll need to set these parameters:

```sql theme={null}
-- Check current settings
SHOW wal_level;
SHOW max_replication_slots;
SHOW max_wal_senders;
```

If not already configured, update your PostgreSQL configuration:

```sql theme={null}
ALTER SYSTEM SET wal_level = logical;
ALTER SYSTEM SET max_replication_slots = 10;
ALTER SYSTEM SET max_wal_senders = 10;
```

Restart your PostgreSQL instance for the changes to take effect.

## 3. Create a Xata project and branch

In the Console, create a new project and then click the **Create main branch** button to create the PostgreSQL instance.

For streaming replication, consider using at least 1 replica to ensure high availability during continuous synchronization. Select an instance size that can handle your expected write throughput.

> **Note:** Streaming replication maintains a persistent connection to your source database. Ensure your network allows stable, long-lived connections between Xata and your PostgreSQL instance.

## 4. Configure the Xata CLI

Authenticate the CLI by running:

```sh theme={null}
xata auth login
```

Initialize the project by running:

```sh theme={null}
xata init
```

## 5. Configure streaming replication

Generate a configuration for the streaming process:

```bash theme={null}
xata clone config --source-url $CONN_STRING
```

Where `CONN_STRING` is your PostgreSQL connection string with replication permissions.

The configuration prompt will ask you to:

* Select tables to replicate
* Set up transformation pipelines i.e. anonymization rules

This creates a configuration file at `.xata/clone.yaml` that you can further customize.

## 6. Initialize and start streaming

```bash theme={null}
xata clone stream --source-url $CONN_STRING
```

This command will:

* Create an initial snapshot of your specified tables
* Set up the streaming pipeline
* Begin continuous replication

## 7. Advanced configuration

### Filtering specific tables

To stream only specific tables, use the `--filter-tables` flag:

```bash theme={null}
xata clone stream --source-url $CONN_STRING \
  --filter-tables "users.*,orders.*,products.*"
```

If this option is not specified it defaults to `*.*`

### Custom transformations

Edit your `.xata/clone.yaml` file to add custom transformations:

```yaml theme={null}
transforms:
  - table: users
    columns:
      - name: email
        transformer: mask_email
      - name: phone
        transformer: redact
  - table: orders
    columns:
      - name: credit_card
        transformer: mask_credit_card
```

### Streaming without DDL tracking

DDL tracking via event triggers requires elevated privileges (e.g., `rds_superuser` on AWS RDS/Aurora, or the `postgres` role on Google Cloud SQL). Most managed PostgreSQL services support this — see the [pgstream setup guides](/docs/opensource/pgstream/docs/aws) for provider-specific instructions on configuring the right roles.

However, if your database user does not have the required privileges to create event triggers, you can use the `--skip-ddl-tracking` flag:

```bash theme={null}
# First, create a replication slot on your source database
psql $CONN_STRING -c "SELECT pg_create_logical_replication_slot('xata_replication_slot', 'pgoutput');"

# Then start streaming with DDL tracking disabled
xata clone stream --source-url $CONN_STRING \
  --skip-ddl-tracking \
  --replication-slot xata_replication_slot
```

When using `--skip-ddl-tracking`:

* You must manually create a replication slot on the source database before streaming
* DDL changes (schema modifications) will not be automatically tracked
* The replication slot name must be provided via the `--replication-slot` flag

## 10. Handling failures and recovery

If the streaming connection is interrupted, the replication slot ensures no data is lost. Simply restart the streaming command:

```bash theme={null}
xata clone stream --source-url $CONN_STRING
```

The process will resume from where it left off, catching up with any changes that occurred during the downtime.
However, if the too much lag accumulates then the Postgres server might slow down as it has to do both catching up on the lag and its normal operations.

If you terminate the `xata clone stream` process and do not wish to run streaming replication again, clean up the replication slot and
other `pgstream` objects using `xata stream destroy` command.

Not cleaning up the replication slot will cause the WAL to be aggregated continuously and that would lead to full disk space. Use options like `max_slot_wal_keep_size`
to keep the max WAL size in check.

## Summary

* You now have real-time streaming replication (Postgres's logical replication) from your PostgreSQL database to Xata
* Changes in your source database are automatically synchronized
* Your data can be anonymized in transit using configurable transformers
* The replication slot ensures no data loss during network interruptions

For more details on advanced streaming configurations and monitoring, see the [clone command documentation](/docs/cli/clone).
