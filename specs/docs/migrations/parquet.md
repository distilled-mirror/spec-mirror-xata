> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Parquet files

> Learn how to migrate your data to Xata from Parquet files exported to AWS S3

## Overview

If your data already lives in [Parquet](https://parquet.apache.org/) files, you can load it into Xata. This is a common path when your source database doesn't expose a live PostgreSQL connection but can export snapshots to object storage instead.

A typical flow is to export your database to Parquet files in an AWS S3 bucket, and then have Xata load those files into your branch.

## Export to Parquet in AWS S3

Both AWS Aurora and AWS RDS can export snapshots to Amazon S3 in Apache Parquet format. AWS writes the export as a set of Parquet files, one set per table, into the S3 bucket you choose.

* **AWS Aurora**: Use *Export to Amazon S3* on a snapshot or cluster. See the [AWS Aurora export documentation](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-export-snapshot.html).
* **AWS RDS**: Use *Export to Amazon S3* on a snapshot. See the [AWS RDS export documentation](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ExportSnapshot.html).

In both cases the export lands in S3 as Parquet files that preserve your table schema and data.

<Tip>
  If you have a live PostgreSQL connection to your source database, you can migrate directly with `xata clone` instead. See the [AWS Aurora](/docs/migrations/aws-aurora) and [AWS RDS](/docs/migrations/aws-rds) guides.
</Tip>

## Import Parquet files into Xata

Importing Parquet files into Xata is currently handled on a case-by-case basis. If you'd like to import Parquet files — whether exported from AWS Aurora, AWS RDS, or another source — [reach out to us](mailto:support@xata.io) and we'll help you load them into your branch.

When you contact us, it helps to share:

* The location of your Parquet files (for example, the S3 bucket and prefix)
* The source database and how the export was produced (AWS Aurora, AWS RDS, or other)
* The approximate size and number of tables
* A schema-only `pg_dump` of your source database:

  ```bash theme={null}
  pg_dump --schema-only --no-owner --no-privileges '<<source-connection-string>>' > schema.sql
  ```

## Verification

After your data is loaded, verify it:

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
