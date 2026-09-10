> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Configuration

⚠️ Be aware that a single source and a single target must be provided or the configuration validation will fail.

## Yaml

The pgstream configuration can be provided as a yaml configuration file, which encapsulates the transformation configuration. The following sample shows the format for all supported fields.

```yaml theme={null}
instrumentation:
  metrics:
    endpoint: "0.0.0.0:4317"
    collection_interval: 60 # collection interval for metrics in seconds. Defaults to 60s
    prometheus:
      enabled: true # exposes a Prometheus scrape endpoint on the health server. Defaults to false
      endpoint: "/metrics" # path the Prometheus endpoint is served on. Defaults to /metrics
  traces:
    endpoint: "0.0.0.0:4317"
    sample_ratio: 0.5 # ratio of traces that will be sampled. Must be between 0.0-1.0, where 0 is no traces sampled, and 1 is all traces sampled.

source:
  postgres:
    url: "postgresql://user:password@localhost:5432/mydatabase"
    mode: snapshot_and_replication # options are replication, snapshot or snapshot_and_replication
    snapshot: # when mode is snapshot or snapshot_and_replication
      mode: full # one of full, schema or data. Defaults to full.
      tables: ["test", "test_schema.Test", "another_schema.*"] # tables to snapshot, can be a list of table names or a pattern
      excluded_tables: ["test_schema.Test"] # tables to exclude for snapshot, wildcards are not supported
      schema_only_tables: ["audit_log", "reports.*"] # tables for which only the schema is snapshotted, no data. Wildcards are supported. Requires snapshot mode full or schema. Tables listed explicitly in `tables` take precedence over a schema-only wildcard match; `excluded_tables` take precedence over the schema-only list. When a snapshot recorder is configured, schema-only snapshots are recorded separately from data snapshots, so moving a table from `schema_only_tables` to `tables` triggers its data snapshot on the next run (and moving it the other way doesn't repeat any work). Exception: when `tables` is the full `*.*` wildcard, its completed snapshot record covers all schemas and a promoted table won't be re-snapshotted automatically; use `pgstream snapshot` with the table listed explicitly instead. Sequence values are copied for schema-only tables, so out-of-band data backfills on the target won't collide with source-generated IDs.
      recorder:
        repeatable_snapshots: true # whether to repeat snapshots that have already been taken. Defaults to false
        postgres_url: "postgresql://user:password@localhost:5432/mytargetdatabase" # URL of the database where the snapshot status is recorded
      snapshot_workers: 4 # number of schemas to be snapshotted in parallel. Defaults to 1
      data: # when mode is full or data
        schema_workers: 4 # number of schema tables to be snapshotted in parallel. Defaults to 4
        table_workers: 4 # number of workers to snapshot a table in parallel. Defaults to 4
        batch_bytes: 83886080 # bytes to read per batch (defaults to 80MiB)
        max_connections: 50 # maximum number of connections that the data snapshot can open to Postgres. Should  be higher or equal than the number of schema/table workers.
      schema: # when mode is full or schema
        pgdump_pgrestore:
          clean_target_db: true # whether to clean the target database before restoring. Defaults to false. Destructive: the target objects are dropped before the data is copied, so a snapshot that fails afterwards leaves the target partially rebuilt with the previous contents gone. See docs/snapshots.md for how to keep a good copy
          create_target_db: true # whether to create the database on the target postgres. Defaults to false
          include_global_db_objects: true # whether to include database global objects, such as extensions or triggers, on the schema snapshot. Defaults to false
          no_owner: false # whether to remove ownership commands from the dump. Defaults to false
          no_privileges: false # whether to prevent dumping privilege commands (grant/revoke). Defaults to false
          role: postgres # role name to be used to create the dump
          roles_snapshot_mode: # no_passwords by default. Can be set to disabled to disable roles snapshotting, or can be set to enabled to include role passwords
          exclude_security_labels: ["anon"] # list of providers whose security labels will be excluded from the snapshot. Wildcard supported.
          refresh_materialized_views: false # whether to refresh materialized views (REFRESH MATERIALIZED VIEW ... WITH DATA) after the table data has been restored. Defaults to false
          index_constraint_session_settings: # optional PostgreSQL name=value session settings applied only while restoring indexes and constraints. Each entry must be a whitespace-free name=value pair. Unset or empty preserves existing behavior
            - maintenance_work_mem=4GB
            - max_parallel_maintenance_workers=4
            # statement_timeout=0 and lock_timeout=0 disable the server-side limits that would otherwise bound a stuck restore; the restore then relies on client-side cancellation. Omit them to keep those safety limits.
            # - statement_timeout=0
            # - lock_timeout=0
            # - synchronous_commit=off # faster restore, but a target crash right after the restore can lose the final index/constraint commits
          dump_file: pg_dump.sql # name of the file where the contents of the schema pg_dump command and output will be written for debugging purposes.
          # Granular object type filtering for schema snapshots. Only one of include_object_types or exclude_object_types can be set.
          # Available categories: tables, sequences, types, indexes, constraints, functions, views, materialized_views, triggers, event_triggers, policies, rules, comments, extensions, collations, text_search
          # include_object_types: ["tables", "sequences", "types"] # only include these object types in the schema snapshot
          # exclude_object_types: ["functions", "views", "triggers"] # exclude these object types from the schema snapshot
      disable_progress_tracking: false # whether to disable progress tracking for the snapshot. Defaults to false
    replication: # when mode is replication or snapshot_and_replication
      replication_slot: "pgstream_mydatabase_slot"
      plugin:
        include_xids: false # whether to include transaction IDs in the replication stream events. Defaults to false.
        add_tables: "public.*" # comma-separated list of tables to include in the wal2json output. Supports wildcards. Filters at the source decode level. Defaults to all tables.
        filter_tables: "pipelines.*,private.*" # comma-separated list of tables to exclude from the wal2json output. Supports wildcards. Filters at the source decode level. Defaults to empty.
    retry_policy: # retry policy for postgres connections, one of exponential or constant or disable_retries
      disable_retries: false
      exponential:
        initial_interval: 500 # initial interval in milliseconds
        max_interval: 10000 # maximum interval in milliseconds
      constant:
        max_retries: 5 # maximum number of retries
        interval: 1000 # interval in milliseconds
  kafka:
    servers: ["localhost:9092"]
    topic:
      name: "mytopic"
    consumer_group:
      id: "mygroup" # id for the kafka consumer group. Defaults to pgstream-consumer-group
      start_offset: "earliest" # options are earliest or latest. Defaults to earliest.
    tls:
      ca_cert: "/path/to/ca.crt" # path to CA certificate
      client_cert: "/path/to/client.crt" # path to client certificate
      client_key: "/path/to/client.key" # path to client key
    backoff:
      disable_retries: false
      exponential:
        max_retries: 5 # maximum number of retries
        initial_interval: 1000 # initial interval in milliseconds
        max_interval: 60000 # maximum interval in milliseconds
      constant:
        max_retries: 5 # maximum number of retries
        interval: 1000 # interval in milliseconds

target:
  postgres:
    url: "postgresql://user:password@localhost:5432/mytargetdatabase"
    max_connections: 50 # maximum number of connections in the writer pool to the target database. Defaults to 50; overrides pool_max_conns in the URL when set. The schema observer keeps its own pool, capped at 16 and never larger than this value, so the process opens at most this many plus 16.
    batch:
      timeout: 1000 # batch timeout in milliseconds. Defaults to 30s
      size: 100 # number of messages in a batch. Defaults to 20000
      max_bytes: 1572864 # max size of batch in bytes (1.5MiB). Defaults to 1.5MiB without bulk enabled, 80MiB with bulk ingest.
      max_queue_bytes: 104857600 # max size of memory guard queue in bytes (100MiB). Defaults to 100MiB
      ignore_send_errors: false # if true, log and ignore errors during batch sending. Warning: can result in consistency errors.
      auto_tune:
        enabled: true # whether to enable auto tuning of batch bytes. Defaults to false
        min_batch_bytes: 1048576 # minimum batch size in bytes (1MB). Defaults to 1MB
        max_batch_bytes: 52428800 # maximum batch size in bytes (50MB). Defaults to 50MB
        convergence_threshold: 0.01 # convergence threshold as a fraction of max batch size. Defaults to 0.01
    disable_triggers: false # whether to disable triggers on the target database. Defaults to false
    on_conflict_action: "nothing" # options are update, nothing or error. Defaults to error
    strict_mode: false # whether to stop on non-internal query failures instead of dropping them. Defaults to false
    bulk_ingest:
      enabled: true # whether to enable bulk ingest on the target postgres, using COPY FROM (supported for insert only workloads)
      copy_workers: 8 # number of concurrent COPY streams per table when bulk ingesting. Defaults to 8. Only applies when bulk_ingest.enabled is true.
    retry_policy: # retry policy for postgres connections, one of exponential or constant or disable_retries.
      disable_retries: false
      exponential:
        initial_interval: 500 # initial interval in milliseconds
        max_interval: 10000 # maximum interval in milliseconds
      constant:
        max_retries: 5 # maximum number of retries
        interval: 1000 # interval in milliseconds
    ignore_ddl: false # whether to disable processing of DDL events on the target Postgres database. Defaults to false. Consider enabling this if source and target roles have different trust levels (see docs/privileges.md).
    # Selective DDL object type filtering for replication. Only one of include_ddl_object_types or exclude_ddl_object_types can be set. Ignored if ignore_ddl is true.
    # Available categories: tables, sequences, types, indexes, constraints, functions, views, materialized_views, triggers, event_triggers, policies, rules, extensions, collations, text_search
    # include_ddl_object_types: ["tables", "sequences", "types"] # only replicate DDL for these object types
    # exclude_ddl_object_types: ["functions", "views", "triggers"] # skip DDL replication for these object types
  kafka:
    servers: ["localhost:9092"]
    topic:
      name: "mytopic" # name of the Kafka topic
      partitions: 1 # number of partitions for the topic. Defaults to 1
      partition_key: "schema" # message key strategy for DML events, one of schema, table or primary_key. Defaults to schema
      replication_factor: 1 # replication factor for the topic. Defaults to 1
      auto_create: true # whether to automatically create the topic if it doesn't exist. Defaults to false
    tls:
      ca_cert: "/path/to/ca.crt" # path to CA certificate
      client_cert: "/path/to/client.crt" # path to client certificate
      client_key: "/path/to/client.key" # path to client key
    batch:
      timeout: 1000 # batch timeout in milliseconds. Defaults to 1s
      size: 100 # number of messages in a batch. Defaults to 100
      max_bytes: 1572864 # max size of batch in bytes (1.5MiB). Defaults to 1.5MiB
      max_queue_bytes: 104857600 # max size of memory guard queue in bytes (100MiB). Defaults to 100MiB
      ignore_send_errors: false # if true, log and ignore errors during batch sending. Warning: can result in consistency errors.
  search:
    engine: "elasticsearch" # options are elasticsearch or opensearch
    url: "http://localhost:9200" # URL of the search engine
    hash_doc_ids: false # if true, hash document IDs using SHA256 to avoid exceeding the 512 byte limit
    batch:
      timeout: 1000 # batch timeout in milliseconds. Defaults to 1s
      size: 100 # number of messages in a batch. Defaults to 100
      max_bytes: 1572864 # max size of batch in bytes (1.5MiB). Defaults to 1.5MiB
      max_queue_bytes: 104857600 # max size of memory guard queue in bytes (100MiB). Defaults to 100MiB
      ignore_send_errors: false # if true, log and ignore errors during batch sending. Warning: can result in consistency errors.
    backoff:
      disable_retries: false
      exponential:
        max_retries: 5 # maximum number of retries
        initial_interval: 1000 # initial interval in milliseconds
        max_interval: 60000 # maximum interval in milliseconds
      constant:
        max_retries: 5 # maximum number of retries
        interval: 1000 # interval in milliseconds
  webhooks:
    subscriptions:
      store:
        url: "postgresql://user:password@localhost:5432/mydatabase" # URL of the database where the webhook subscriptions are stored
        cache:
          enabled: true # whether to enable caching for the subscription store. Defaults to false
          refresh_interval: 60 # interval in seconds to refresh the cache
      server:
        address: "localhost:9090" # address of the subscription server
        read_timeout: 60 # read timeout in seconds. Defaults to 5s
        write_timeout: 60 # write timeout in seconds. Defaults to 10s
    notifier:
      worker_count: 4 # number of notifications to be processed in parallel. Defaults to 10
      client_timeout: 1000 # timeout for the webhook client in milliseconds. Defaults to 10s
  stdout: {} # write WAL events as NDJSON to stdout. Useful for debugging and validating the pipeline without a real target.

modifiers:
  injector:
    enabled: true # whether to inject pgstream metadata into the WAL events. Defaults to false
    source_url: "postgres://postgres:postgres@localhost:5432?sslmode=disable" # optional for postgres sources (defaults to source URL), required for non-postgres sources
  filter: # one of include_tables or exclude_tables; schema_only_tables can be combined with either
    include_tables: # list of tables for which events should be allowed. Tables should be schema qualified. If no schema is provided, the public schema will be assumed. Wildcards "*" are supported.
      - "test"
      - "test_schema.test"
      - "another_schema.*"
    exclude_tables: # list of tables for which events should be skipped. Tables should be schema qualified. If no schema is provided, the public schema will be assumed. Wildcards "*" are supported.
      - "excluded_test"
      - "excluded_schema.test"
      - "another_excluded_schema.*"
    schema_only_tables: # list of tables for which DDL (schema change) events are processed but data (DML) events are skipped. Tables should be schema qualified. If no schema is provided, the public schema will be assumed. Wildcards "*" are supported. Tables listed explicitly in include_tables take precedence over a schema-only wildcard match; exclude_tables take precedence over the schema-only list.
      - "audit_log"
      - "reports.*"
  sanitize:
    strip_null_char_bytes: true # strip null bytes (0x00) from string column values. Defaults to false
  transformations:
    validation_mode: relaxed
    table_transformers:
      - schema: public
        table: test
        column_transformers:
          name:
            name: greenmask_firstname
            dynamic_parameters:
              gender:
                column: sex
```

## Environment Variables

Here's a list of all the environment variables that can be used to configure the individual modules, along with their descriptions and default values.

> Byte-size variables (those ending in `_BYTES`) accept either a plain integer or a human-readable size such as `64MiB` or `1GiB`. Units are case-insensitive and interpreted as binary multiples (`1MiB` = 1048576 bytes). The same applies to the equivalent fields in the YAML configuration.

### Sources

#### Postgres Listener

<AccordionGroup>
  <Accordion title="PGSTREAM_POSTGRES_LISTENER_URL">
    **Default**: N/A\
    **Required**: ✓ Required\
    **Description**: URL of the Postgres database to connect to for replication purposes.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_REPLICATION_SLOT_NAME">
    **Default**: "pgstream\_dbname\_slot"\
    **Required**: Optional\
    **Description**: Name of the Postgres replication slot name.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_REPLICATION_PLUGIN_INCLUDE_XIDS">
    **Default**: False\
    **Required**: Optional\
    **Description**: Whether to include XIDs in the replication events.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_REPLICATION_PLUGIN_ADD_TABLES">
    **Default**: ""\
    **Required**: Optional\
    **Description**: Comma-separated list of tables to include in the wal2json output. Supports wildcards (e.g. `public.*`). Filters at the source decode level, preventing wal2json from generating JSON for excluded tables.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_REPLICATION_PLUGIN_FILTER_TABLES">
    **Default**: ""\
    **Required**: Optional\
    **Description**: Comma-separated list of tables to exclude from the wal2json output. Supports wildcards (e.g. `pipelines.*,private.*`). Filters at the source decode level, preventing wal2json from generating JSON for excluded tables.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_MODE">
    **Default**: "full"\
    **Required**: Optional\
    **Description**: Mode in which the snapshot will be run. It can be one of `schema`, `data` or `full` (both schema and data).
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_TABLES">
    **Default**: ""\
    **Required**: Optional\
    **Description**: Tables for which there will be an initial snapshot generated. The syntax supports wildcards. Tables without a schema defined will be applied the public schema. Example: for `public.test_table` and all tables in the `test_schema` schema, the value would be the following: `"test_table test_schema.\*"`
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_EXCLUDED_TABLES">
    **Default**: ""\
    **Required**: Optional\
    **Description**: Tables that will be excluded in the snapshot process. The syntax does not support wildcards. Tables without a schema defined will be applied the public schema.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_SCHEMA_ONLY_TABLES">
    **Default**: ""\
    **Required**: Optional\
    **Description**: Tables for which only the schema will be snapshotted, skipping their data. The syntax supports wildcards. Tables without a schema defined will be applied the public schema. Requires snapshot mode `full` or `schema`. Tables explicitly listed in the snapshot tables take precedence over a schema-only wildcard match; excluded tables take precedence over the schema-only list.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_SCHEMA_WORKERS">
    **Default**: 4\
    **Required**: Optional\
    **Description**: Number of tables per schema that will be processed in parallel by the snapshotting process.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_TABLE_WORKERS">
    **Default**: 4\
    **Required**: Optional\
    **Description**: Number of concurrent workers that will be used per table by the snapshotting process.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_BATCH_BYTES">
    **Default**: 83886080 (80MiB)\
    **Required**: Optional\
    **Description**: Max batch size in bytes to be read and processed by each table worker at a time. The number of pages in the select queries will be based on this value.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_WORKERS">
    **Default**: 1\
    **Required**: Optional\
    **Description**: Number of schemas that will be processed in parallel by the snapshotting process.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_MAX_CONNECTIONS">
    **Default**: 50\
    **Required**: Optional\
    **Description**: Maximum number of Postgres connections that will be opened by the snapshotting process. This value shouldn't be lower than the number of schema/table workers selected.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_CLEAN_TARGET_DB">
    **Default**: False\
    **Required**: Optional\
    **Description**: When using `pg_dump`/`pg_restore` to snapshot schema for Postgres targets, option to issue commands to DROP all the objects that will be restored. Destructive: the DROPs are applied before the table data is copied, so a snapshot that fails afterwards leaves the target partially rebuilt with the previous contents already gone. See [resetting the target](/docs/opensource/pgstream/docs/snapshots#️-resetting-the-target-destroys-it-before-the-new-data-lands).
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_INCLUDE_GLOBAL_DB_OBJECTS">
    **Default**: False\
    **Required**: Optional\
    **Description**: When using `pg_dump`/`pg_restore` to snapshot schema for Postgres targets, option to snapshot all global database objects outside of the selected schema (such as extensions, triggers, etc).
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_CREATE_TARGET_DB">
    **Default**: False\
    **Required**: Optional\
    **Description**: When using `pg_dump`/`pg_restore` to snapshot schema for Postgres targets, option to create the database being restored.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_NO_OWNER">
    **Default**: False\
    **Required**: Optional\
    **Description**: When using `pg_dump`/`pg_restore` to snapshot schema for Postgres targets, do not output commands to set ownership of objects to match the original database.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_NO_PRIVILEGES">
    **Default**: False\
    **Required**: Optional\
    **Description**: When using `pg_dump`/`pg_restore` to snapshot schema for Postgres targets, do not output privilege related commands (grant/revoke).
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_EXCLUDED_SECURITY_LABELS">
    **Default**: \[]\
    **Required**: Optional\
    **Description**: When using `pg_dump`/`pg_restore` to snapshot schema for Postgres targets, list of providers whose security labels will be excluded.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_REFRESH_MATERIALIZED_VIEWS">
    **Default**: False\
    **Required**: Optional\
    **Description**: When using `pg_dump`/`pg_restore` to snapshot schema for Postgres targets, whether to refresh materialized views (REFRESH MATERIALIZED VIEW ... WITH DATA) after the table data has been restored.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_INDEX_CONSTRAINT_SESSION_SETTINGS">
    **Default**: \[]\
    **Required**: Optional\
    **Description**: Space-separated PostgreSQL `name=value` session settings applied only while restoring indexes and constraints, for example `maintenance_work_mem=4GB max_parallel_maintenance_workers=4`. Each setting must be a whitespace-free `name=value` pair; invalid entries fail at startup. Unset or empty preserves existing behavior.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_INCLUDE_OBJECT_TYPES">
    **Default**: \[]\
    **Required**: Optional\
    **Description**: When using `pg_dump`/`pg_restore` to snapshot schema for Postgres targets, list of object type categories to include in the schema snapshot. Everything else is excluded. Mutually exclusive with `PGSTREAM_POSTGRES_SNAPSHOT_EXCLUDE_OBJECT_TYPES`. See [object type filtering](#object-type-filtering).
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_EXCLUDE_OBJECT_TYPES">
    **Default**: \[]\
    **Required**: Optional\
    **Description**: When using `pg_dump`/`pg_restore` to snapshot schema for Postgres targets, list of object type categories to exclude from the schema snapshot. Mutually exclusive with `PGSTREAM_POSTGRES_SNAPSHOT_INCLUDE_OBJECT_TYPES`. See [object type filtering](#object-type-filtering).
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_ROLE">
    **Default**: ""\
    **Required**: Optional\
    **Description**: When using `pg_dump`/`pg_restore` to snapshot schema for Postgres targets, role name to be used to create the dump.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_ROLES_SNAPSHOT_MODE">
    **Default**: "no\_passwords"\
    **Required**: Optional\
    **Description**: When using `pg_dump`/`pg_restore` to snapshot schema for Postgres targets, controls how roles are snapshotted. Possible values: "enabled" (snapshot all roles including passwords), "disabled" (do not snapshot roles), "no\_passwords" (snapshot roles but exclude passwords).
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_SCHEMA_DUMP_FILE">
    **Default**: ""\
    **Required**: Optional\
    **Description**: When using `pg_dump`/`pg_restore` to snapshot schema for Postgres targets, file where the contents of the schema pg\_dump command and output will be written for debugging purposes.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_STORE_URL">
    **Default**: ""\
    **Required**: Optional\
    **Description**: Postgres URL for the database where the snapshot requests and their status will be tracked. A table `snapshot_requests` will be created under a `pgstream` schema.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_STORE_REPEATABLE">
    **Default**: False (run), True (snapshot)\
    **Required**: Optional\
    **Description**: Allow to repeat snapshots requests that have been already completed successfully. If using the run command, initial snapshots won't be repeatable by default. If the snapshot command is used instead, the snapshot will be repeatable by default.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_SNAPSHOT_DISABLE_PROGRESS_TRACKING">
    **Default**: False\
    **Required**: Optional\
    **Description**: Whether to disable progress tracking for the snapshot.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_LISTENER_EXP_BACKOFF_INITIAL_INTERVAL">
    **Default**: 500ms\
    **Required**: Optional\
    **Description**: Initial interval for the exponential backoff policy to be applied to the Postgres connection retries.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_LISTENER_EXP_BACKOFF_MAX_INTERVAL">
    **Default**: 10s\
    **Required**: Optional\
    **Description**: Max interval for the exponential backoff policy to be applied to the Postgres connection retries.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_LISTENER_EXP_BACKOFF_MAX_RETRIES">
    **Default**: 20\
    **Required**: Optional\
    **Description**: Max retries for the exponential backoff policy to be applied to the Postgres connection retries.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_LISTENER_BACKOFF_INTERVAL">
    **Default**: 0\
    **Required**: Optional\
    **Description**: Constant interval for the backoff policy to be applied to the Postgres connection retries.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_LISTENER_BACKOFF_MAX_RETRIES">
    **Default**: 0\
    **Required**: Optional\
    **Description**: Max retries for the backoff policy to be applied to the Postgres connection retries.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_LISTENER_DISABLE_RETRIES">
    **Default**: False\
    **Required**: Optional\
    **Description**: Disable any retry policy.
  </Accordion>
</AccordionGroup>

One of exponential/constant/disable retries retry policies can be provided for the Postgres connection retry strategy. If none is provided, the exponential defaults apply.

#### Kafka Listener

<AccordionGroup>
  <Accordion title="PGSTREAM_KAFKA_READER_SERVERS">
    **Default**: N/A\
    **Required**: ✓ Required\
    **Description**: URLs for the Kafka servers to connect to.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_TOPIC_NAME">
    **Default**: N/A\
    **Required**: ✓ Required\
    **Description**: Name of the Kafka topic to read from.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_READER_CONSUMER_GROUP_ID">
    **Default**: N/A\
    **Required**: ✓ Required\
    **Description**: Name of the Kafka consumer group for the WAL Kafka reader.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_READER_CONSUMER_GROUP_START_OFFSET">
    **Default**: Earliest\
    **Required**: Optional\
    **Description**: Kafka offset from which the consumer will start if there's no offset available for the consumer group.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_TLS_ENABLED">
    **Default**: False\
    **Required**: Optional\
    **Description**: Enable TLS connection to the Kafka servers.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_TLS_CA_CERT_FILE">
    **Default**: ""\
    **Required**: Optional\
    **Description**: Path to the CA PEM certificate to use for Kafka TLS authentication.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_TLS_CLIENT_CERT_FILE">
    **Default**: ""\
    **Required**: Optional\
    **Description**: Path to the client PEM certificate to use for Kafka TLS client authentication.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_TLS_CLIENT_KEY_FILE">
    **Default**: ""\
    **Required**: Optional\
    **Description**: Path to the client PEM private key to use for Kafka TLS client authentication.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_COMMIT_EXP_BACKOFF_INITIAL_INTERVAL">
    **Default**: 0\
    **Required**: Optional\
    **Description**: Initial interval for the exponential backoff policy to be applied to the Kafka commit retries.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_COMMIT_EXP_BACKOFF_MAX_INTERVAL">
    **Default**: 0\
    **Required**: Optional\
    **Description**: Max interval for the exponential backoff policy to be applied to the Kafka commit retries.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_COMMIT_EXP_BACKOFF_MAX_RETRIES">
    **Default**: 0\
    **Required**: Optional\
    **Description**: Max retries for the exponential backoff policy to be applied to the Kafka commit retries.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_COMMIT_BACKOFF_INTERVAL">
    **Default**: 0\
    **Required**: Optional\
    **Description**: Constant interval for the backoff policy to be applied to the Kafka commit retries.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_COMMIT_BACKOFF_MAX_RETRIES">
    **Default**: 0\
    **Required**: Optional\
    **Description**: Max retries for the backoff policy to be applied to the Kafka commit retries.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_COMMIT_DISABLE_RETRIES">
    **Default**: False\
    **Required**: Optional\
    **Description**: Disable any retry policy.
  </Accordion>
</AccordionGroup>

One of exponential/constant backoff policies can be provided for the Kafka committing retry strategy. If none is provided, no retries apply.

### Targets

#### Kafka Batch Writer

<AccordionGroup>
  <Accordion title="PGSTREAM_KAFKA_WRITER_SERVERS">
    **Default**: N/A\
    **Required**: ✓ Required\
    **Description**: URLs for the Kafka servers to connect to.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_TOPIC_NAME">
    **Default**: N/A\
    **Required**: ✓ Required\
    **Description**: Name of the Kafka topic to write to.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_TOPIC_PARTITIONS">
    **Default**: 1\
    **Required**: Optional\
    **Description**: Number of partitions created for the Kafka topic if auto create is enabled.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_TOPIC_PARTITION_KEY">
    **Default**: schema\
    **Required**: Optional\
    **Description**: Message key strategy for DML events, one of `schema`, `table` or `primary_key`. See the ordering trade-offs below.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_TOPIC_REPLICATION_FACTOR">
    **Default**: 1\
    **Required**: Optional\
    **Description**: Replication factor used when creating the Kafka topic if auto create is enabled.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_TOPIC_AUTO_CREATE">
    **Default**: False\
    **Required**: Optional\
    **Description**: Auto creation of configured Kafka topic if it doesn't exist.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_TLS_ENABLED">
    **Default**: False\
    **Required**: Optional\
    **Description**: Enable TLS connection to the Kafka servers.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_TLS_CA_CERT_FILE">
    **Default**: ""\
    **Required**: Optional\
    **Description**: Path to the CA PEM certificate to use for Kafka TLS authentication.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_TLS_CLIENT_CERT_FILE">
    **Default**: ""\
    **Required**: Optional\
    **Description**: Path to the client PEM certificate to use for Kafka TLS client authentication.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_TLS_CLIENT_KEY_FILE">
    **Default**: ""\
    **Required**: Optional\
    **Description**: Path to the client PEM private key to use for Kafka TLS client authentication.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_WRITER_BATCH_TIMEOUT">
    **Default**: 1s\
    **Required**: Optional\
    **Description**: Max time interval at which the batch sending to Kafka is triggered.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_WRITER_BATCH_BYTES">
    **Default**: 1572864\
    **Required**: Optional\
    **Description**: Max size in bytes for a given batch. When this size is reached, the batch is sent to Kafka.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_WRITER_BATCH_SIZE">
    **Default**: 100\
    **Required**: Optional\
    **Description**: Max number of messages to be sent per batch. When this size is reached, the batch is sent to Kafka.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_WRITER_BATCH_IGNORE_SEND_ERRORS">
    **Default**: False\
    **Required**: Optional\
    **Description**: Whether to ignore errors encountered while sending batches to the target.
  </Accordion>

  <Accordion title="PGSTREAM_KAFKA_WRITER_MAX_QUEUE_BYTES">
    **Default**: 104857600 (100MiB)\
    **Required**: Optional\
    **Description**: Max memory used by the Kafka batch writer for inflight batches.
  </Accordion>
</AccordionGroup>

The partition key determines which partition an event is routed to, and therefore which events are consumed in order relative to each other:

* `schema` (default): all events for a schema go to the same partition, guaranteeing ordering per schema, including between DDL and DML events. Parallelism is capped at the number of distinct schemas, so extra partitions don't help a single-schema database.
* `table`: events are keyed by schema qualified table name, guaranteeing ordering per table. DDL events remain keyed by schema, so schema changes can be consumed out of order relative to the DML events of the tables they affect.
* `primary_key`: events are keyed by schema qualified table name plus the row primary key values, guaranteeing ordering per row and allowing full use of the topic partitions. Requires the injector (`PGSTREAM_INJECTOR_STORE_POSTGRES_URL`) to identify primary key columns; events without an identifiable primary key fall back to `table` keying. As with `table`, DDL events remain keyed by schema and can be consumed out of order relative to DML.

#### Search Batch Indexer

<AccordionGroup>
  <Accordion title="PGSTREAM_OPENSEARCH_STORE_URL">
    **Default**: N/A\
    **Required**: ✓ Required\
    **Description**: URL for the opensearch store to connect to (at least one of the URLs must be provided).
  </Accordion>

  <Accordion title="PGSTREAM_ELASTICSEARCH_STORE_URL">
    **Default**: N/A\
    **Required**: ✓ Required\
    **Description**: URL for the elasticsearch store to connect to (at least one of the URLs must be provided).
  </Accordion>

  <Accordion title="PGSTREAM_SEARCH_INDEXER_HASH_DOC_IDS">
    **Default**: False\
    **Required**: Optional\
    **Description**: If true, hash document IDs using SHA256 to avoid exceeding the Elasticsearch 512 byte limit.
  </Accordion>

  <Accordion title="PGSTREAM_SEARCH_INDEXER_BATCH_TIMEOUT">
    **Default**: 1s\
    **Required**: Optional\
    **Description**: Max time interval at which the batch sending to the search store is triggered.
  </Accordion>

  <Accordion title="PGSTREAM_SEARCH_INDEXER_BATCH_SIZE">
    **Default**: 100\
    **Required**: Optional\
    **Description**: Max number of messages to be sent per batch. When this size is reached, the batch is sent to the search store.
  </Accordion>

  <Accordion title="PGSTREAM_SEARCH_INDEXER_BATCH_IGNORE_SEND_ERRORS">
    **Default**: False\
    **Required**: Optional\
    **Description**: Whether to ignore errors encountered while sending batches to the target.
  </Accordion>

  <Accordion title="PGSTREAM_SEARCH_INDEXER_MAX_QUEUE_BYTES">
    **Default**: 104857600 (100MiB)\
    **Required**: Optional\
    **Description**: Max memory used by the search batch indexer for inflight batches.
  </Accordion>

  <Accordion title="PGSTREAM_SEARCH_STORE_EXP_BACKOFF_INITIAL_INTERVAL">
    **Default**: 1s\
    **Required**: Optional\
    **Description**: Initial interval for the exponential backoff policy to be applied to the search store operation retries.
  </Accordion>

  <Accordion title="PGSTREAM_SEARCH_STORE_EXP_BACKOFF_MAX_INTERVAL">
    **Default**: 1min\
    **Required**: Optional\
    **Description**: Max interval for the exponential backoff policy to be applied to the search store operation retries.
  </Accordion>

  <Accordion title="PGSTREAM_SEARCH_STORE_EXP_BACKOFF_MAX_RETRIES">
    **Default**: 0\
    **Required**: Optional\
    **Description**: Max retries for the exponential backoff policy to be applied to the search store operation retries.
  </Accordion>

  <Accordion title="PGSTREAM_SEARCH_STORE_BACKOFF_INTERVAL">
    **Default**: 0\
    **Required**: Optional\
    **Description**: Constant interval for the backoff policy to be applied to the search store operation retries.
  </Accordion>

  <Accordion title="PGSTREAM_SEARCH_STORE_BACKOFF_MAX_RETRIES">
    **Default**: 0\
    **Required**: Optional\
    **Description**: Max retries for the backoff policy to be applied to the search store operation retries.
  </Accordion>

  <Accordion title="PGSTREAM_SEARCH_STORE_DISABLE_RETRIES">
    **Default**: False\
    **Required**: Optional\
    **Description**: Disable any retry policy.
  </Accordion>
</AccordionGroup>

One of exponential/constant backoff policies can be provided for the search indexer cleanup retry strategy. If none is provided, no retries apply.

One of exponential/constant/disable retries backoff policies can be provided for the search store retry strategy. If none is provided, a default exponential backoff policy applies.

#### Stdout Writer

<AccordionGroup>
  <Accordion title="PGSTREAM_STDOUT_WRITER_ENABLED">
    **Default**: False\
    **Required**: Optional\
    **Description**: Set to true to enable the stdout writer target. WAL events are written as NDJSON (one JSON object per line) to stdout.
  </Accordion>
</AccordionGroup>

#### Webhook Notifier

<AccordionGroup>
  <Accordion title="PGSTREAM_WEBHOOK_SUBSCRIPTION_STORE_URL">
    **Default**: N/A\
    **Required**: ✓ Required\
    **Description**: URL for the webhook subscription store to connect to.
  </Accordion>

  <Accordion title="PGSTREAM_WEBHOOK_SUBSCRIPTION_STORE_CACHE_ENABLED">
    **Default**: False\
    **Required**: Optional\
    **Description**: Caching applied to the subscription store retrieval queries.
  </Accordion>

  <Accordion title="PGSTREAM_WEBHOOK_SUBSCRIPTION_STORE_CACHE_REFRESH_INTERVAL">
    **Default**: 60s\
    **Required**: Optional\
    **Description**: Interval at which the subscription store cache will be refreshed. Indicates max cache staleness.
  </Accordion>

  <Accordion title="PGSTREAM_WEBHOOK_NOTIFIER_MAX_QUEUE_BYTES">
    **Default**: 104857600 (100MiB)\
    **Required**: Optional\
    **Description**: Max memory used by the webhook notifier for inflight notifications.
  </Accordion>

  <Accordion title="PGSTREAM_WEBHOOK_NOTIFIER_WORKER_COUNT">
    **Default**: 10\
    **Required**: Optional\
    **Description**: Max number of concurrent workers that will send webhook notifications for a given WAL event.
  </Accordion>

  <Accordion title="PGSTREAM_WEBHOOK_NOTIFIER_CLIENT_TIMEOUT">
    **Default**: 10s\
    **Required**: Optional\
    **Description**: Max time the notifier will wait for a response from a webhook URL before timing out.
  </Accordion>

  <Accordion title="PGSTREAM_WEBHOOK_NOTIFIER_EXP_BACKOFF_INITIAL_INTERVAL">
    **Default**: 1s\
    **Required**: Optional\
    **Description**: Initial interval for the exponential backoff policy to be applied to failed webhook deliveries.
  </Accordion>

  <Accordion title="PGSTREAM_WEBHOOK_NOTIFIER_EXP_BACKOFF_MAX_INTERVAL">
    **Default**: 30s\
    **Required**: Optional\
    **Description**: Max interval for the exponential backoff policy to be applied to failed webhook deliveries.
  </Accordion>

  <Accordion title="PGSTREAM_WEBHOOK_NOTIFIER_EXP_BACKOFF_MAX_RETRIES">
    **Default**: 3\
    **Required**: Optional\
    **Description**: Max retries for the exponential backoff policy to be applied to failed webhook deliveries.
  </Accordion>

  <Accordion title="PGSTREAM_WEBHOOK_NOTIFIER_BACKOFF_INTERVAL">
    **Default**: 0\
    **Required**: Optional\
    **Description**: Constant interval for the backoff policy to be applied to failed webhook deliveries.
  </Accordion>

  <Accordion title="PGSTREAM_WEBHOOK_NOTIFIER_BACKOFF_MAX_RETRIES">
    **Default**: 0\
    **Required**: Optional\
    **Description**: Max retries for the backoff policy to be applied to failed webhook deliveries.
  </Accordion>

  <Accordion title="PGSTREAM_WEBHOOK_NOTIFIER_DISABLE_RETRIES">
    **Default**: False\
    **Required**: Optional\
    **Description**: Disable any retry policy for failed webhook deliveries.
  </Accordion>

  <Accordion title="PGSTREAM_WEBHOOK_NOTIFIER_STRICT_MODE">
    **Default**: False\
    **Required**: Optional\
    **Description**: Whether to stop the pipeline on a permanently failing webhook delivery instead of dropping it and continuing. It defaults to false.
  </Accordion>

  <Accordion title="PGSTREAM_WEBHOOK_SUBSCRIPTION_SERVER_ADDRESS">
    **Default**: ":9900"\
    **Required**: Optional\
    **Description**: Address for the subscription server to listen on.
  </Accordion>

  <Accordion title="PGSTREAM_WEBHOOK_SUBSCRIPTION_SERVER_READ_TIMEOUT">
    **Default**: 5s\
    **Required**: Optional\
    **Description**: Max duration for reading an entire server request, including the body before timing out.
  </Accordion>

  <Accordion title="PGSTREAM_WEBHOOK_SUBSCRIPTION_SERVER_WRITE_TIMEOUT">
    **Default**: 10s\
    **Required**: Optional\
    **Description**: Max duration before timing out writes of the response. It is reset whenever a new request's header is read.
  </Accordion>
</AccordionGroup>

One of exponential/constant/disable retries backoff policies can be provided for the webhook notifier retry strategy. If none is provided, a default exponential backoff policy applies (1s initial interval, 30s max interval, 3 max retries). A 2xx response is treated as success; a 429 or 5xx response is retried; any other response (e.g. 4xx) is treated as a permanent failure and is not retried. A delivery that keeps failing with a retryable error after exhausting retries is not checkpointed, so it will be retried again after a restart. A delivery that permanently fails is logged and dropped instead by default, so a single misconfigured subscriber does not block delivery to other subscribers; set `strict_mode` to stop the pipeline on permanent failures instead, matching the at-least-once guarantee at the cost of availability.

#### Postgres Batch Writer

<AccordionGroup>
  <Accordion title="PGSTREAM_POSTGRES_WRITER_TARGET_URL">
    **Default**: N/A\
    **Required**: ✓ Required\
    **Description**: URL for the PostgreSQL store to connect to
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_MAX_CONNECTIONS">
    **Default**: 50\
    **Required**: Optional\
    **Description**: Maximum number of connections in the writer pool to the target PostgreSQL database. Overrides `pool_max_conns` in the target URL when set. The schema observer keeps its own pool, capped at 16 and never larger than this value, so the process opens at most this many plus 16.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_BATCH_TIMEOUT">
    **Default**: 30s\
    **Required**: Optional\
    **Description**: Max time interval at which the batch sending to PostgreSQL is triggered.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_BATCH_SIZE">
    **Default**: 20000\
    **Required**: Optional\
    **Description**: Max number of messages to be sent per batch. When this size is reached, the batch is sent to PostgreSQL.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_MAX_QUEUE_BYTES">
    **Default**: 104857600 (100MiB)\
    **Required**: Optional\
    **Description**: Max memory used by the postgres batch writer for inflight batches.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_BATCH_BYTES">
    **Default**: 1572864 (1.5MiB), 83886080 (80MiB) with bulk enabled\
    **Required**: Optional\
    **Description**: Max size in bytes for a given batch. When this size is reached, the batch is sent to PostgreSQL.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_BATCH_IGNORE_SEND_ERRORS">
    **Default**: False\
    **Required**: Optional\
    **Description**: Whether to ignore errors encountered while sending events to the target.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_DISABLE_TRIGGERS">
    **Default**: False(run), True(snapshot)\
    **Required**: Optional\
    **Description**: Option to disable triggers on the target PostgreSQL database while performing the snaphot/replication streaming. It defaults to false when using the run command, and to true when using the snapshot command.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_ON_CONFLICT_ACTION">
    **Default**: error\
    **Required**: Optional\
    **Description**: Action to apply to inserts on conflict. Options are `nothing`, `update` or `error`.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_STRICT_MODE">
    **Default**: False\
    **Required**: Optional\
    **Description**: Whether to stop on non-internal query failures instead of dropping them and continuing. It defaults to false.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_BULK_INGEST_ENABLED">
    **Default**: False(run), True(snapshot)\
    **Required**: Optional\
    **Description**: Whether to use COPY FROM on insert only workloads. It defaults to false when using the run command, and to true when using the snapshot command.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_BULK_INGEST_COPY_WORKERS">
    **Default**: 8\
    **Required**: Optional\
    **Description**: Number of concurrent COPY streams per table when bulk ingesting. Only applies when bulk ingest is enabled.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_EXP_BACKOFF_INITIAL_INTERVAL">
    **Default**: 500ms\
    **Required**: Optional\
    **Description**: Initial interval for the exponential backoff policy to be applied to the Postgres connection retries.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_EXP_BACKOFF_MAX_INTERVAL">
    **Default**: 10s\
    **Required**: Optional\
    **Description**: Max interval for the exponential backoff policy to be applied to the Postgres connection retries.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_EXP_BACKOFF_MAX_RETRIES">
    **Default**: 20\
    **Required**: Optional\
    **Description**: Max retries for the exponential backoff policy to be applied to the Postgres connection retries.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_BACKOFF_INTERVAL">
    **Default**: 0\
    **Required**: Optional\
    **Description**: Constant interval for the backoff policy to be applied to the Postgres connection retries.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_BACKOFF_MAX_RETRIES">
    **Default**: 0\
    **Required**: Optional\
    **Description**: Max retries for the backoff policy to be applied to the Postgres connection retries.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_DISABLE_RETRIES">
    **Default**: False\
    **Required**: Optional\
    **Description**: Disable any retry policy.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_IGNORE_DDL">
    **Default**: False\
    **Required**: Optional\
    **Description**: Disable processing of DDL events on the target Postgres database. Consider enabling if source and target roles have different trust levels (see [privileges](/docs/opensource/pgstream/docs/privileges)).
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_INCLUDE_DDL_OBJECT_TYPES">
    **Default**: \[]\
    **Required**: Optional\
    **Description**: List of object type categories for which DDL is replicated. DDL for everything else is skipped. Mutually exclusive with `PGSTREAM_POSTGRES_WRITER_EXCLUDE_DDL_OBJECT_TYPES`, and ignored when `PGSTREAM_POSTGRES_WRITER_IGNORE_DDL` is true. See [object type filtering](#object-type-filtering).
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_EXCLUDE_DDL_OBJECT_TYPES">
    **Default**: \[]\
    **Required**: Optional\
    **Description**: List of object type categories for which DDL replication is skipped. Mutually exclusive with `PGSTREAM_POSTGRES_WRITER_INCLUDE_DDL_OBJECT_TYPES`, and ignored when `PGSTREAM_POSTGRES_WRITER_IGNORE_DDL` is true. See [object type filtering](#object-type-filtering).
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_BATCH_AUTO_TUNE_ENABLE">
    **Default**: False\
    **Required**: Optional\
    **Description**: Whether to enable auto tuning of batch bytes.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_BATCH_AUTO_TUNE_MIN_BYTES">
    **Default**: 1048576 (1MB)\
    **Required**: Optional\
    **Description**: Minimum batch size in bytes used by the auto tune process.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_BATCH_AUTO_TUNE_MAX_BYTES">
    **Default**: 52428800 (50MB)\
    **Required**: Optional\
    **Description**: Maximum batch size in bytes used by the auto tune process.
  </Accordion>

  <Accordion title="PGSTREAM_POSTGRES_WRITER_BATCH_AUTO_TUNE_CONVERGENCE_THRESHOLD">
    **Default**: 0.01 (1%)\
    **Required**: Optional\
    **Description**: Minimum search space width (as a fraction of the max batch bytes) at which the auto tune process stops searching for a better batch size.
  </Accordion>
</AccordionGroup>

One of exponential/constant/disable retries retry policies can be provided for the Postgres connection retry strategy. If none is provided, the exponential defaults apply.

#### Modifiers

#### Injector

<AccordionGroup>
  <Accordion title="PGSTREAM_INJECTOR_STORE_POSTGRES_URL">
    **Default**: N/A\
    **Required**: ✓ Required\
    **Description**: PostgreSQL URL for DDL replication.
  </Accordion>
</AccordionGroup>

#### Transformer

<AccordionGroup>
  <Accordion title="PGSTREAM_TRANSFORMER_RULES_FILE">
    **Default**: N/A\
    **Required**: Optional\
    **Description**: Filepath pointing to the yaml file containing the transformer rules.
  </Accordion>
</AccordionGroup>

#### Filter

<AccordionGroup>
  <Accordion title="PGSTREAM_FILTER_INCLUDE_TABLES">
    **Default**: N/A\
    **Required**: Optional\
    **Description**: List of schema qualified tables for which the WAL events should be processed. If no schema is provided, `public` schema will be assumed. Wildcards are supported.
  </Accordion>

  <Accordion title="PGSTREAM_FILTER_EXCLUDE_TABLES">
    **Default**: N/A\
    **Required**: Optional\
    **Description**: List of schema qualified tables for which the WAL events should be skipped. If no schema is provided, `public` schema will be assumed. Wildcards are supported.
  </Accordion>

  <Accordion title="PGSTREAM_FILTER_SCHEMA_ONLY_TABLES">
    **Default**: N/A\
    **Required**: Optional\
    **Description**: List of schema qualified tables for which DDL (schema change) events are processed but data (DML) events are skipped. If no schema is provided, `public` schema will be assumed. Wildcards are supported. Can be combined with either the include or the exclude list.
  </Accordion>
</AccordionGroup>

#### Sanitizer

<AccordionGroup>
  <Accordion title="PGSTREAM_PROCESSOR_SANITIZE_STRIP_NULL_CHAR_BYTES">
    **Default**: false\
    **Required**: Optional\
    **Description**: Strip null bytes (0x00) from string column values. Useful when the source database contains null bytes that are not allowed by the target.
  </Accordion>
</AccordionGroup>

### Instrumentation

#### Metrics

<AccordionGroup>
  <Accordion title="PGSTREAM_METRICS_ENDPOINT">
    **Default**: N/A\
    **Required**: Optional\
    **Description**: Endpoint where the pgstream metrics will be pushed to via OTLP. Not required to use Prometheus.
  </Accordion>

  <Accordion title="PGSTREAM_METRICS_COLLECTION_INTERVAL">
    **Default**: 60s\
    **Required**: Optional\
    **Description**: Interval at which the pgstream metrics will be collected and exported via OTLP.
  </Accordion>

  <Accordion title="PGSTREAM_METRICS_PROMETHEUS_ENABLED">
    **Default**: False\
    **Required**: Optional\
    **Description**: Exposes a Prometheus scrape endpoint on the health server. Requires the health endpoint enabled.
  </Accordion>

  <Accordion title="PGSTREAM_METRICS_PROMETHEUS_ENDPOINT">
    **Default**: /metrics\
    **Required**: Optional\
    **Description**: Path the Prometheus endpoint is served on.
  </Accordion>
</AccordionGroup>

#### Traces

<AccordionGroup>
  <Accordion title="PGSTREAM_TRACES_ENDPOINT">
    **Default**: N/A\
    **Required**: Optional\
    **Description**: Endpoint where the pgstream traces will be exported to.
  </Accordion>

  <Accordion title="PGSTREAM_TRACES_SAMPLE_RATIO">
    **Default**: 0\
    **Required**: Optional\
    **Description**: Ratio for the trace sampling. Value must be between 0.0 and 1.0, where 0.0 is no traces sampled, and 1.0 is all traces sampled.
  </Accordion>
</AccordionGroup>

#### Health endpoint

Exposes `/health` (liveness, always 200), `/ready` (readiness, pings the source postgres database when configured), and `/status` (current pipeline phase: `snapshot` or `replication`). Only the `run` and `snapshot` commands start the server. Responses are JSON.

When `instrumentation.metrics.prometheus.enabled` (`PGSTREAM_METRICS_PROMETHEUS_ENABLED`) is true, the server also exposes a Prometheus scrape endpoint (default path `/metrics`, configurable via `instrumentation.metrics.prometheus.endpoint` / `PGSTREAM_METRICS_PROMETHEUS_ENDPOINT`). The endpoint returns `404` when Prometheus is disabled. This lets you scrape pgstream's metrics directly, without deploying an OTel collector.

<AccordionGroup>
  <Accordion title="PGSTREAM_HEALTH_CHECK_ENABLED">
    **Default**: False\
    **Required**: Optional\
    **Description**: Enable the health endpoint server.
  </Accordion>

  <Accordion title="PGSTREAM_HEALTH_CHECK_ADDRESS">
    **Default**: localhost:9910\
    **Required**: Optional\
    **Description**: Address the health server listens on. Use `:9910` or `0.0.0.0:9910` to expose externally (e.g. in k8s pods).
  </Accordion>
</AccordionGroup>

## Object type filtering

Both the schema snapshot and DDL replication can be restricted to a subset of database object types. Each side is configured independently, with an allowlist (`include_*`) or a denylist (`exclude_*`); setting both on the same side is rejected at startup, and so is an unknown category name.

The available categories are:

<AccordionGroup>
  <Accordion title="tables">
    **Schema snapshot**: ✅\
    **DDL replication**: ✅
  </Accordion>

  <Accordion title="sequences">
    **Schema snapshot**: ✅\
    **DDL replication**: ✅
  </Accordion>

  <Accordion title="types">
    **Schema snapshot**: ✅\
    **DDL replication**: ✅
  </Accordion>

  <Accordion title="indexes">
    **Schema snapshot**: ✅\
    **DDL replication**: ✅
  </Accordion>

  <Accordion title="constraints">
    **Schema snapshot**: ✅\
    **DDL replication**: ✅
  </Accordion>

  <Accordion title="functions">
    **Schema snapshot**: ✅\
    **DDL replication**: ✅
  </Accordion>

  <Accordion title="views">
    **Schema snapshot**: ✅\
    **DDL replication**: ✅
  </Accordion>

  <Accordion title="materialized_views">
    **Schema snapshot**: ✅\
    **DDL replication**: ✅
  </Accordion>

  <Accordion title="triggers">
    **Schema snapshot**: ✅\
    **DDL replication**: ✅
  </Accordion>

  <Accordion title="event_triggers">
    **Schema snapshot**: ✅\
    **DDL replication**: ✅
  </Accordion>

  <Accordion title="policies">
    **Schema snapshot**: ✅\
    **DDL replication**: ✅
  </Accordion>

  <Accordion title="rules">
    **Schema snapshot**: ✅\
    **DDL replication**: ✅
  </Accordion>

  <Accordion title="extensions">
    **Schema snapshot**: ✅\
    **DDL replication**: ✅
  </Accordion>

  <Accordion title="collations">
    **Schema snapshot**: ✅\
    **DDL replication**: ✅
  </Accordion>

  <Accordion title="text_search">
    **Schema snapshot**: ✅\
    **DDL replication**: ✅
  </Accordion>

  <Accordion title="comments">
    **Schema snapshot**: ✅\
    **DDL replication**: ❌
  </Accordion>
</AccordionGroup>

Schemas themselves are never filtered out, since the remaining objects need their namespaces to exist.

For DDL replication, a single statement can create objects of several types at once (`CREATE TABLE` with a primary key produces both a table and an index). Such an event is only skipped when *all* of its objects belong to excluded categories.

> ⚠️ Filtering does not resolve dependencies between object types. Excluding a category that surviving objects depend on (for example excluding `types` while keeping tables with columns of those types) will make the snapshot or the DDL replay fail. Use this with a good understanding of your schema.
