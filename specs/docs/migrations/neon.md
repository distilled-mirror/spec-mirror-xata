> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Neon

> Learn how to migrate your Neon PostgreSQL database to Xata using xata clone

## Prerequisites

* Access to Neon Console
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

## Find the Neon connection string

Neon connection strings typically look something like this:

```
postgresql://neondb_owner:your_password@hostname.c-3.us-east-1.aws.neon.tech/dbname?sslmode=require&channel_binding=require
```

To find yours, follow these steps:

1. Go to Neon Console → Your Project
2. Select the branch you want to copy from
3. Click "Connect" to get the "Connect to your database" modal
4. Unset the Connection pooling toggle
5. Select "Connection string" in the dropdown
6. Click "Copy snippet" to copy it, including the password

## Initialize Xata project

Go to a local folder that you want to save the Xata configuration to and initialize it like this:

```bash theme={null}
xata init
```

Select your organization and project you want to use. For the database name you can either choose a new one (e.g `xata`) or use the same that you have in Neon. If the database doesn't exist, it will be created.

In case you need to redo this step, you can reset the configuration by deleting the `.xata/project.json` file and running `xata init` again.

## Start the migration

Begin the data transfer:

```bash theme={null}
xata clone start --validation-mode relaxed --source-url '<<neon-connection-string>>'
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

## Update your application

If you're using the `@neondatabase/serverless` driver, you can continue using it with Xata—just update your connection string. Xata implements the same serverless protocol.

1. Get your Xata connection string:
   ```bash theme={null}
   xata branch url
   ```

2. Update your environment variable:
   ```bash theme={null}
   DATABASE_URL=<your-xata-connection-string>
   ```

Your existing code works without changes:

```typescript theme={null}
import { neon } from '@neondatabase/serverless';

const sql = neon(process.env.DATABASE_URL);
const users = await sql`SELECT * FROM users`;
```

<Tip>
  For more details on using the serverless driver with Xata, see the [Serverless Proxy](/docs/core-concepts/serverless-proxy) documentation.
</Tip>
