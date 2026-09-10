> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Greenmask (subsets)

> Learn how to import a referentially-consistent subset of your PostgreSQL database to Xata using Greenmask.

## When to use this guide

Use Greenmask when you want to import a **representative slice** of your production database into a Xata branch. For example to evaluate Xata against your real schema and workload without copying 100% of your data. Greenmask follows your foreign-key graph from a chosen "entrypoint" table and exports only the rows that hang off it (and their dependencies), preserving referential integrity.

If you want a **full copy** of your database into Xata, use `xata clone` instead — see the provider-specific guides for [AWS RDS](/docs/migrations/aws-rds), [AWS Aurora](/docs/migrations/aws-aurora), [Azure](/docs/migrations/azure), [GCP Cloud SQL](/docs/migrations/gcp-cloudsql), [Neon](/docs/migrations/neon), [Supabase](/docs/migrations/supabase), and [self-hosted PostgreSQL](/docs/migrations/self-hosted). Greenmask is for the subset case.

## Prerequisites

* A PostgreSQL source you can reach over the network (public endpoint or via your own VPN/SSH tunnel)
* Xata account and project setup with a running `main` branch
* Knowledge of your schema's foreign-key structure — at minimum, which table you want as the subset "entrypoint" (typically `users`, `tenants`, `accounts`, or similar)

## Install and configure the Xata CLI

Install the Xata CLI:

```bash theme={null}
curl -fsSL https://xata.io/install.sh | bash
```

Authenticate with your Xata account:

```bash theme={null}
xata auth login
```

## Install Greenmask

Download a pre-built binary from the [Greenmask releases page](https://github.com/GreenmaskIO/greenmask/releases). This guide is written against **v0.2.19**.

```bash theme={null}
curl -fsSL https://github.com/GreenmaskIO/greenmask/releases/download/v0.2.19/greenmask-linux-amd64.tar.gz \
  | tar -xz
./greenmask --version
```

Replace `linux-amd64` with `darwin-arm64` etc. as needed.

## Find your source connection string

The exact format depends on your provider (see the other guides in this folder for AWS RDS, Neon, etc.). A generic PostgreSQL connection string looks like:

```
postgresql://your_username:your_password@your-host:5432/your_database?sslmode=require
```

## Initialize Xata project

Go to a local folder that you want to save the Xata configuration to and initialize it like this:

```bash theme={null}
xata init
```

Select your organization and project you want to use. For the database name you can either choose a new one (e.g `xata`) or use the same that you have in your source database. If the database doesn't exist, it will be created.

In case you need to redo this step, you can reset the configuration by deleting the `.xata/project.json` file and running `xata init` again.

Once initialized, you can get the branch connection string Greenmask will restore into with:

```bash theme={null}
xata branch url
```

Treat it as sensitive: it grants full write access.

## Prepare the target branch

`pg_dump --schema=...` (which Greenmask uses under the hood) doesn't export PostgreSQL extension definitions from the `public` schema. Create any extensions your source schema uses on the target *before* the restore:

```bash theme={null}
psql "$(xata branch url)" <<'SQL'
CREATE EXTENSION IF NOT EXISTS pg_trgm;
CREATE EXTENSION IF NOT EXISTS unaccent;
-- add any others your source schema uses
SQL
```

To list the extensions your source uses:

```bash theme={null}
psql '<<source-connection-string>>' -c "\dx"
```

## Write your subset config

Create `greenmask.yml` in your working directory. Replace `your_schema`, `your_entrypoint_table`, and the predicate to match your schema. The example below assumes a multi-tenant SaaS shape where `tenants.id` is the subset root.

```yaml theme={null}
common:
  pg_bin_path: "/usr/lib/postgresql/16/bin"   # or wherever your local pg_dump lives
  tmp_dir: "/tmp"

log:
  level: "info"
  format: "json"

storage:
  type: "directory"
  directory:
    path: "./dump"

dump:
  pg_dump_options:
    dbname: "<<source-connection-string>>"
    jobs: 4
    schema: "your_schema"
    load-via-partition-root: true

  transformation:
    - schema: "your_schema"
      name: "tenants"
      subset_conds:
        - "your_schema.tenants.id <= 100"

restore:
  pg_restore_options:
    dbname: "<<xata-branch-connection-string>>"
    jobs: 4
    exit-on-error: true
    no-owner: true
    no-privileges: true
```

<Tip>
  There's no single "right" predicate. Start with a `subset_conds` value that returns a small slice (for example `tenants.id <= 10`), run the dump, and check the resulting row counts. Widen or narrow the condition and re-run until the subset is the size and shape you want — a tight predicate keeps the first few iterations fast while you dial it in.
</Tip>

## Run the dump

```bash theme={null}
./greenmask --config greenmask.yml dump
```

## Restore into Xata

```bash theme={null}
./greenmask --config greenmask.yml restore latest
```

If you want to retry, drop the schema on the target first:

```bash theme={null}
psql "$(xata branch url)" -c "DROP SCHEMA IF EXISTS your_schema CASCADE;"
```

then re-create the extensions (see the "Prepare the target branch" step) and re-run `restore`.

## Verify

After restore, verify the import:

```bash theme={null}
psql "$(xata branch url)"
```

```sql theme={null}
-- List tables
\dt your_schema.*

-- Check counts on key tables
SELECT count(*) FROM your_schema.your_table;

-- Sample some rows
SELECT * FROM your_schema.your_table LIMIT 10;
```

### Foreign-key integrity check

For a subset to be useful, every foreign-key reference in the target must point to a row that exists. To audit this, compare the foreign-key constraint count between source and target — they must match:

```bash theme={null}
psql '<<source-connection-string>>' -At -c "
  SELECT count(*) FROM pg_constraint c
  JOIN pg_namespace n ON n.oid = c.connamespace
  WHERE c.contype='f' AND n.nspname='your_schema';"

psql "$(xata branch url)" -At -c "
  SELECT count(*) FROM pg_constraint c
  JOIN pg_namespace n ON n.oid = c.connamespace
  WHERE c.contype='f' AND n.nspname='your_schema';"
```

### Limits

* Greenmask runs `pg_dump`/`pg_restore` under the hood, so the same version compatibility rules apply: your local `pg_dump` major version must be ≥ the source server's, and the same major version as your Xata branch (currently PostgreSQL 18).
* Sources with custom PostgreSQL extensions that Xata doesn't support will fail during restore. Verify with `psql -c "\dx"` against your source first.
* Greenmask's subset model works best when your data has a clear "root" entity (a tenant, a user, an organization). Schemas without one will need more configuration work.

## Going further

Greenmask supports rich PII transformations (anonymising emails, hashing names, redacting fields) on top of subsetting. Those are useful if you want to use your imported subset for dev/test as well as evaluation. See the [Greenmask transformer docs](https://docs.greenmask.io/latest/built_in_transformers/) for the full list.

For full data migration (not subsets), see the other guides in this folder.
