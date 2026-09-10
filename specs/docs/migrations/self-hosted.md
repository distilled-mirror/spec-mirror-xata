> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Self-Hosted PostgreSQL

> Learn how to migrate your self-hosted PostgreSQL database to Xata using xata clone.

## Prerequisites

* Self-hosted PostgreSQL instance
* Access to PostgreSQL configuration files
* Xata account and project setup with a running `main` branch

## Install and configure the Xata CLI

Install the Xata CLI:

```bash theme={null}
curl -fsSL https://xata.io/install.sh | bash
```

Authenticate with your Xata account:

```bash theme={null}
xata auth login
```

## Find the connection string

Form your PostgreSQL connection string like this:

```
postgresql://your_username:your_password@your-server-ip:5432/your_database
```

If your PostgreSQL server requires SSL:

```
postgresql://your_username:your_password@your-server-ip:5432/your_database?sslmode=require
```

## Initialize Xata project

Go to a local folder that you want to save the Xata configuration to and initialize it like this:

```bash theme={null}
xata init
```

Select your organization and project you want to use. For the database name you can either choose a new one (e.g `xata`) or use the same that you have in the source PostgreSQL. If the database doesn't exist, it will be created.

In case you need to redo this step, you can reset the configuration by deleting the `.xata/project.json` file and running `xata init` again.

## Start the migration

Begin the data transfer:

```bash theme={null}
xata clone start --validation-mode relaxed --source-url '<<postgres-connection-string>>'
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
