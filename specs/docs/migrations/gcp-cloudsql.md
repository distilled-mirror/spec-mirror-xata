> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# GCP Cloud SQL

> Learn how to migrate your GCP Cloud SQL PostgreSQL database to Xata using xata clone

## Prerequisites

* GCP Cloud SQL PostgreSQL instance
* Access to Google Cloud Console
* Xata account and project setup with a running `main` branch
* Network access to your Cloud SQL instance

## Install and configure the Xata CLI

Install the Xata CLI:

```bash theme={null}
curl -fsSL https://xata.io/install.sh | bash
```

Authenticate with your Xata account:

```bash theme={null}
xata auth login
```

## Find the Cloud SQL connection string

Form the PostgreSQL connection string like this:

```
postgresql://your_username:your_password@cloudsql-ip-address:5432/dbname?sslmode=require
```

You can find the cloudsql-ip-address in the Cloud SQL console under "Connect to this instance".

## Initialize Xata project

Go to a local folder that you want to save the Xata configuration to and initialize it like this:

```bash theme={null}
xata init
```

Select your organization and project you want to use. For the database name you can either choose a new one (e.g `xata`) or use the same that you have in Cloud SQL. If the database doesn't exist, it will be created.

In case you need to redo this step, you can reset the configuration by deleting the `.xata/project.json` file and running `xata init` again.

## Start the migration

Begin the data transfer:

```bash theme={null}
xata clone start --validation-mode relaxed --source-url '<<cloud-connection-string>>'
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
