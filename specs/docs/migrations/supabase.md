> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Supabase

> Learn how to migrate your Supabase PostgreSQL database to Xata using xata clone

## Prerequisites

* Access to Supabase Dashboard
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

## Find the Supabase connection string

Supabase connection strings typically look like this:

```
postgresql://postgres:[YOUR_PASSWORD]@db.hostnameid.supabase.co:5432/postgres
```

To find yours, follow these steps:

1. Go to Supabase Dashboard → Your Project
2. Click "Connect" to bring up the "Connect to your project"
3. Copy the connection string
4. The password is not shown in the Supabase dashboard for security reasons. You can copy it from you records, or you can reset it if you have to.

## Initialize Xata project

Go to a local folder that you want to save the Xata configuration to and initialize it like this:

```bash theme={null}
xata init
```

Select your organization and project you want to use. For the database name you can either choose a new one (e.g `xata`) or use the same that you have in Supabase. If the database doesn't exist, it will be created.

In case you need to redo this step, you can reset the configuration by deleting the `.xata/project.json` file and running `xata init` again.

## Start the migration

Begin the data transfer:

```bash theme={null}
PGSTREAM_POSTGRES_SNAPSHOT_TABLES='public.*' xata clone start --validation-mode relaxed --source-url '<<supabase-connection-string>>'
```

Adjust the `PGSTREAM_POSTGRES_SNAPSHOT_TABLES` env variable with the schema names you want to transfer. It is needed here to avoid copying Supabase specific schemas like `storage` and `realtime`.

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
