> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# AWS RDS

> Learn how to migrate your AWS RDS PostgreSQL database to Xata using xata clone

## Prerequisites

* AWS RDS PostgreSQL instance
* Access to AWS Console
* Xata account and project setup with a running `main` branch
* Network access to your RDS instance

## Install and Configure the Xata CLI

Install the Xata CLI:

```bash theme={null}
curl -fsSL https://xata.io/install.sh | bash
```

Authenticate with your Xata account:

```bash theme={null}
xata auth login
```

## Find the RDS connection string

RDS connection strings typically look something like this:

```
postgresql://your_username:your_password@rds-endpoint:5432/dbname
```

You can find the rds-endpoint in the AWS RDS Console under "Connectivity & security".

<Tip>
  Don't have a live connection, or prefer to export a snapshot to Amazon S3? You can also migrate from [Parquet files](/docs/migrations/parquet) exported to S3.
</Tip>

## Initialize Xata project

Go to a local folder that you want to save the Xata configuration to and initialize it like this:

```bash theme={null}
xata init
```

Select your organization and project you want to use. For the database name you can either choose a new one (e.g `xata`) or use the same that you have in RDS. If the database doesn't exist, it will be created.

In case you need to redo this step, you can reset the configuration by deleting the `.xata/project.json` file and running `xata init` again.

## Start the migration

Begin the data transfer:

```bash theme={null}
xata clone start --validation-mode relaxed --source-url '<<rds-connection-string>>'
```

Depending on how large the source database is, this step might take a while. A progress bar will show you the progress in the CLI.

## Verification

After migration, verify your data:

1. **Connect to Xata Branch**:
   ```bash theme={null}
   psql `xata branch url`
   ```

2. **Check Data Integrity**:
   ```sql theme={null}
   -- List tables
   \dt

   -- Compare row counts
   SELECT COUNT(*) FROM your_table;

   -- Check sample data
   SELECT * FROM your_table LIMIT 10;
   ```
