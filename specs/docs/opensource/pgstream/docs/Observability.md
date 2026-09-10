> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Observability

pgstream provides comprehensive observability through [OpenTelemetry](https://opentelemetry.io/) metrics and distributed tracing. This document outlines the available metrics and how to use them for monitoring your data streaming pipeline.

## Overview

pgstream instruments all major components of the data streaming pipeline with metrics and traces using OpenTelemetry. The instrumentation covers:

* WAL replication and processing
* Snapshot generation
* Kafka read/write operations
* PostgreSQL query operations
* Search indexing operations
* Data transformations
* [Go runtime metrics](https://pkg.go.dev/go.opentelemetry.io/contrib/instrumentation/runtime) (memory, GC, goroutines, etc.)
* [Go profiling](https://go.dev/blog/pprof) (CPU, memory, goroutines, etc.)

## Quick Start with Local Setup

pgstream includes a local observability setup using [SigNoz](https://signoz.io/) with a pre-configured [dashboard](https://github.com/xataio/pgstream/blob/main/build/docker/signoz/dashboards/pgstream.json) that visualizes all the metrics described below.

To get started:

```bash theme={null}
# Start the observability stack
docker-compose -f build/docker/docker-compose-signoz.yml --profile instrumentation up

# To start it alongside a different profile
docker-compose -f build/docker/docker-compose.yml -f build/docker/docker-compose-signoz.yml --profile pg2pg --profile instrumentation up

# Access SigNoz dashboard
open http://localhost:8080

# Import the pgstream dashboard
# Dashboard configuration is available in ./build/docker/signoz/dashboards/pgstream.json
```

The dashboard includes panels for all key metrics, alerting rules, and provides a comprehensive view of your pgstream deployment health and performance.

## Metrics

All metrics follow the `pgstream.*` naming convention and include relevant attributes for filtering and aggregation.

### WAL Replication

<AccordionGroup>
  <Accordion title="pgstream.replication.lag">
    **Type**: ObservableGauge\
    **Unit**: bytes\
    **Description**: Replication lag in bytes accrued by the pgstream WAL consumer
  </Accordion>
</AccordionGroup>

**Attributes:** None

**Usage:** Monitor replication health and detect lag buildup that could indicate performance issues.

**⚠️ Important:** This metric only tracks pgstream's consumer lag. It's strongly recommended to also monitor your source PostgreSQL metrics, particularly the built-in replication lag metrics (`pg_stat_replication.flush_lag`, `pg_stat_replication.replay_lag`) to get a complete picture of replication health.

### Pipeline Phase

<AccordionGroup>
  <Accordion title="pgstream.pipeline.phase">
    **Type**: ObservableGauge\
    **Unit**: 1\
    **Description**: Reports `1` for the active pipeline phase and `0` for the others (\`phase=snapshot\\
  </Accordion>
</AccordionGroup>

**Attributes:**

* `phase`: Phase this data point refers to (`snapshot` or `replication`)

**Usage:** Distinguish whether a running process is performing the initial snapshot or streaming logical replication, without scraping logs. Both `phase="snapshot"` and `phase="replication"` series are reported on every collection, with `1` marking the active phase and `0` the inactive one, so queries like `sum by (phase)` stay unambiguous across transitions. Nothing is reported until the first phase transition. The gauge flips to `replication` when logical replication starts. The same value is also exposed via the health server `GET /status` endpoint when health checks are enabled.

### WAL Event Processing

<AccordionGroup>
  <Accordion title="pgstream.target.processing.lag">
    **Type**: Histogram\
    **Unit**: ns\
    **Description**: Time between WAL event creation and processing
  </Accordion>

  <Accordion title="pgstream.target.processing.latency">
    **Type**: Histogram\
    **Unit**: ns\
    **Description**: Time taken to process a WAL event
  </Accordion>
</AccordionGroup>

**Attributes:**

* `target`: Target type (e.g., "postgres", "kafka", "search")

**Usage:** Monitor end-to-end processing latency for each target and identify bottlenecks in the processing pipeline.

### Snapshot Operations

<AccordionGroup>
  <Accordion title="pgstream.snapshot.generator.latency">
    **Type**: Histogram\
    **Unit**: ms\
    **Description**: Time taken to snapshot a source PostgreSQL database
  </Accordion>
</AccordionGroup>

**Attributes:**

* `snapshot_schema`: List of schemas being snapshotted
* `snapshot_tables`: List of tables being snapshotted

**Usage:** Monitor snapshot performance and identify slow-running snapshot operations.

### Kafka Operations

#### Writer Metrics

<AccordionGroup>
  <Accordion title="pgstream.kafka.writer.batch.size">
    **Type**: Histogram\
    **Unit**: messages\
    **Description**: Distribution of message batch sizes
  </Accordion>

  <Accordion title="pgstream.kafka.writer.batch.bytes">
    **Type**: Histogram\
    **Unit**: bytes\
    **Description**: Distribution of message batch sizes in bytes
  </Accordion>

  <Accordion title="pgstream.kafka.writer.latency">
    **Type**: Histogram\
    **Unit**: ms\
    **Description**: Time taken to send messages to Kafka
  </Accordion>
</AccordionGroup>

#### Reader Metrics

<AccordionGroup>
  <Accordion title="pgstream.kafka.reader.msg.bytes">
    **Type**: Histogram\
    **Unit**: bytes\
    **Description**: Distribution of message sizes read from Kafka
  </Accordion>

  <Accordion title="pgstream.kafka.reader.fetch.latency">
    **Type**: Histogram\
    **Unit**: ms\
    **Description**: Time taken to fetch messages from Kafka
  </Accordion>

  <Accordion title="pgstream.kafka.reader.commit.latency">
    **Type**: Histogram\
    **Unit**: ms\
    **Description**: Time taken to commit offsets to Kafka
  </Accordion>

  <Accordion title="pgstream.kafka.reader.commit.batch.size">
    **Type**: Histogram\
    **Unit**: offsets\
    **Description**: Distribution of offset batch sizes committed
  </Accordion>
</AccordionGroup>

**Attributes:** None

**Usage:** Monitor Kafka throughput, latency, and batch efficiency to optimize performance.

### PostgreSQL Operations

<AccordionGroup>
  <Accordion title="pgstream.postgres.querier.latency">
    **Type**: Histogram\
    **Unit**: ms\
    **Description**: Time taken to perform PostgreSQL queries
  </Accordion>
</AccordionGroup>

**Attributes:**

* `query_type`: Type of SQL operation (e.g., "SELECT", "INSERT", "UPDATE", "DELETE", "tx")
* `query`: The actual SQL query (for non-transaction operations)

**Usage:** Monitor database performance and identify slow queries.

#### Writer Metrics

<AccordionGroup>
  <Accordion title="pgstream.postgres.writer.dropped_queries">
    **Type**: ObservableCounter\
    **Unit**: queries\
    **Description**: Number of queries silently dropped due to non-internal (DATALOSS) failures in drop-and-continue mode
  </Accordion>
</AccordionGroup>

**Attributes:**

* `writer_type`: The postgres writer that dropped the query (one of "postgres\_batch\_writer", "postgres\_bulk\_ingest\_writer")

**Usage:** Alert on any non-zero value: each drop means a failing change was skipped and the checkpoint advanced past it, so the target replica may have diverged from the source. Enable `strict_mode` (`PGSTREAM_POSTGRES_WRITER_STRICT_MODE`) to make such failures stop the pipeline instead of being dropped.

### Search Operations

<AccordionGroup>
  <Accordion title="pgstream.search.store.doc.errors">
    **Type**: Counter\
    **Unit**: errors\
    **Description**: Count of failed document indexing operations
  </Accordion>
</AccordionGroup>

**Attributes:**

* `severity`: Error severity level (one of "NONE", "DATALOSS", "IGNORED", "RETRIABLE")

**Usage:** Monitor search indexing health and error rates.

### Data Transformations

<AccordionGroup>
  <Accordion title="pgstream.transformer.latency">
    **Type**: Histogram\
    **Unit**: ms\
    **Description**: Time taken to transform data values
  </Accordion>
</AccordionGroup>

**Attributes:**

* `transformer_type`: Type of transformer being used

**Usage:** Monitor transformation performance and identify slow transformers.

### Go Runtime Metrics

pgstream automatically collects Go runtime metrics using the [OpenTelemetry Go runtime instrumentation](https://pkg.go.dev/go.opentelemetry.io/contrib/instrumentation/runtime). These metrics are essential for monitoring application health and performance:

<AccordionGroup>
  <Accordion title="runtime.go.mem.heap_alloc">
    **Type**: Gauge\
    **Unit**: bytes\
    **Description**: Bytes of allocated heap objects
  </Accordion>

  <Accordion title="runtime.go.mem.heap_idle">
    **Type**: Gauge\
    **Unit**: bytes\
    **Description**: Bytes in idle (unused) heap spans
  </Accordion>

  <Accordion title="runtime.go.mem.heap_inuse">
    **Type**: Gauge\
    **Unit**: bytes\
    **Description**: Bytes in in-use heap spans
  </Accordion>

  <Accordion title="runtime.go.mem.heap_objects">
    **Type**: Gauge\
    **Unit**: objects\
    **Description**: Number of allocated heap objects
  </Accordion>

  <Accordion title="runtime.go.mem.heap_released">
    **Type**: Gauge\
    **Unit**: bytes\
    **Description**: Bytes of physical memory returned to the OS
  </Accordion>

  <Accordion title="runtime.go.mem.heap_sys">
    **Type**: Gauge\
    **Unit**: bytes\
    **Description**: Bytes of heap memory obtained from the OS
  </Accordion>

  <Accordion title="runtime.go.gc.count">
    **Type**: Counter\
    **Unit**: collections\
    **Description**: Number of completed GC cycles
  </Accordion>

  <Accordion title="runtime.go.gc.pause_ns">
    **Type**: Histogram\
    **Unit**: ns\
    **Description**: Amount of nanoseconds in GC stop-the-world pauses
  </Accordion>

  <Accordion title="runtime.go.goroutines">
    **Type**: Gauge\
    **Unit**: goroutines\
    **Description**: Number of goroutines that currently exist
  </Accordion>

  <Accordion title="runtime.go.lookups">
    **Type**: Counter\
    **Unit**: lookups\
    **Description**: Number of pointer lookups performed by the runtime
  </Accordion>

  <Accordion title="runtime.go.mem.gc_sys">
    **Type**: Gauge\
    **Unit**: bytes\
    **Description**: Bytes of memory in garbage collection metadata
  </Accordion>
</AccordionGroup>

**Usage:** Monitor application resource usage, detect memory leaks, track GC performance, and identify goroutine leaks.

## Configuration

### Enabling Metrics

Configure metrics collection in your pgstream configuration file:

```yaml theme={null}
instrumentation:
  metrics:
    endpoint: "0.0.0.0:4317"
    collection_interval: 60 # collection interval for metrics in seconds. Defaults to 60s
  traces:
    endpoint: "0.0.0.0:4317"
    sample_ratio: 0.5 # ratio of traces that will be sampled. Must be between 0.0-1.0, where 0 is no traces sampled, and 1 is all traces sampled.
```

Or using environment variables:

```sh theme={null}
PGSTREAM_METRICS_ENDPOINT="http://localhost:4317"
PGSTREAM_METRICS_COLLECTION_INTERVAL=60s
PGSTREAM_TRACES_ENDPOINT="http://localhost:4317"
PGSTREAM_TRACES_SAMPLE_RATIO=0.5
```

### Prometheus Scraping (Pull-based)

If you'd rather scrape metrics directly than deploy an OTel collector, enable the Prometheus exporter. It's served from the existing [health endpoint](/docs/opensource/pgstream/docs/configuration#instrumentation) server, so no separate port or process is needed:

```yaml theme={null}
instrumentation:
  health:
    enabled: true
    address: "0.0.0.0:9910" # defaults to localhost:9910
  metrics:
    prometheus:
      enabled: true
      endpoint: "/metrics" # defaults to /metrics
```

Or using environment variables:

```sh theme={null}
PGSTREAM_HEALTH_CHECK_ENABLED=true
PGSTREAM_HEALTH_CHECK_ADDRESS="0.0.0.0:9910"
PGSTREAM_METRICS_PROMETHEUS_ENABLED=true
PGSTREAM_METRICS_PROMETHEUS_ENDPOINT="/metrics"
```

This works standalone — `metrics.endpoint` is not required, so you get pgstream's metrics (including the [Go runtime metrics](#go-runtime-metrics)) without an OTLP collector in the loop. It can also be combined with `metrics.endpoint` to export to both a Prometheus scraper and an OTLP collector at the same time.

## Monitoring Dashboards

### Pre-built pgstream Dashboard

The included SigNoz dashboard provides:

* **Overview Panel**: System health and key metrics at a glance
* **Replication Health**: Both pgstream and PostgreSQL replication metrics
* **Processing Performance**: Latency and throughput across all components
* **Error Tracking**: Error rates and failure patterns
* **Resource Utilization**: Memory, CPU, and network usage
* **Go Runtime Health**: Memory usage, GC performance, and goroutine tracking

<img width="1656" height="1251" alt="pgstream_signoz_dashboard" src="https://github.com/user-attachments/assets/c08e5029-494d-4dc6-94e1-6ce467d3abce" />

### Key Performance Indicators (KPIs)

1. **Application Health**

   * `runtime.go.mem.heap_alloc` - Memory usage trends
   * `runtime.go.goroutines` - Goroutine count (detect leaks)
   * `runtime.go.gc.pause_ns` - GC pause times (performance impact)

2. **Replication Health**

   * `pgstream.replication.lag` - Should remain low and stable
   * `pgstream.target.processing.lag` - End-to-end processing delay
   * **PostgreSQL replication lag** - Monitor source database metrics

3. **Throughput**

   * `pgstream.kafka.writer.batch.size` - Messages per batch
   * `pgstream.kafka.writer.batch.bytes` - Bytes per batch
   * `rate(pgstream.postgres.querier.latency_count[5m])` - Query rate

4. **Latency**

   * `pgstream.target.processing.latency` (p95, p99) - Processing latency percentiles
   * `pgstream.kafka.writer.latency` (p95, p99) - Kafka write latency
   * `pgstream.postgres.querier.latency` (p95, p99) - Database query latency

5. **Error Rates**
   * `rate(pgstream.search.store.doc.errors[5m])` - Search indexing errors
   * Error logs from distributed traces

## Source PostgreSQL Monitoring

**⚠️ Critical:** pgstream metrics only track the consumer-side lag. For complete replication monitoring, you must also monitor your source PostgreSQL instance:

### Essential PostgreSQL Metrics

```sql theme={null}
-- WAL generation rate
SELECT
    pg_current_wal_lsn(),
    pg_wal_lsn_diff(pg_current_wal_lsn(), restart_lsn) / 1024 / 1024 AS wal_mb;

-- Replication slot lag for all consumers
SELECT
    slot_name,
    active,
    pg_wal_lsn_diff(pg_current_wal_lsn(), restart_lsn) / 1024 / 1024 AS lag_mb
FROM pg_replication_slots;
```

## Distributed Tracing

pgstream automatically creates traces for all major operations. Traces include:

* Complete request flows from WAL event to target system
* Database query execution details
* Kafka produce/consume operations
* Transformation pipeline execution
* Error context and stack traces

Use the included SigNoz setup or tools like Jaeger/Zipkin to visualize and analyze trace data for debugging and performance optimization.

<img width="1454" height="1022" alt="pgstream_signoz_tracing" src="https://github.com/user-attachments/assets/595cfff7-d1a2-419c-afd0-739e851e1fa7" />

## Profiling

pgstream includes built-in Go profiling capabilities using Go's [`net/http/pprof`](https://pkg.go.dev/net/http/pprof) package for performance analysis and debugging.

### Enabling Profiling

Profiling can be enabled using the `--profile` flag on supported commands:

```bash theme={null}
# Enable profiling for snapshot operations
pgstream snapshot --profile --config config.yaml

# Enable profiling for run operations
pgstream run --profile --config config.yaml
```

### Profiling Modes

#### 1. HTTP Endpoint (All Commands)

When `--profile` is enabled, pgstream exposes a profiling HTTP server at `localhost:6060` with the following endpoints:

<AccordionGroup>
  <Accordion title="http://localhost:6060/debug/pprof/">
    **Description**: Profile index page
  </Accordion>

  <Accordion title="http://localhost:6060/debug/pprof/profile">
    **Description**: CPU profile (30-second sample)
  </Accordion>

  <Accordion title="http://localhost:6060/debug/pprof/heap">
    **Description**: Memory heap profile
  </Accordion>

  <Accordion title="http://localhost:6060/debug/pprof/goroutine">
    **Description**: Goroutine profile
  </Accordion>

  <Accordion title="http://localhost:6060/debug/pprof/allocs">
    **Description**: Memory allocation profile
  </Accordion>

  <Accordion title="http://localhost:6060/debug/pprof/block">
    **Description**: Block profile
  </Accordion>

  <Accordion title="http://localhost:6060/debug/pprof/mutex">
    **Description**: Mutex profile
  </Accordion>

  <Accordion title="http://localhost:6060/debug/pprof/trace">
    **Description**: Execution trace
  </Accordion>
</AccordionGroup>

#### 2. File Output (Snapshot Command Only)

For the `snapshot` command, profiling also generates profile files:

* **`cpu.prof`** - CPU profile for the entire snapshot operation
* **`mem.prof`** - Memory profile taken at the end of the operation

### Using Profiling Data

#### With Go's pprof Tool

1. **CPU Hotspots**: Identify functions consuming the most CPU time

   ```bash theme={null}
   go tool pprof -http=:8080 http://localhost:6060/debug/pprof/profile
   ```

2. **Memory Usage**: Find memory allocation patterns and potential leaks

   ```bash theme={null}
   go tool pprof -http=:8080 http://localhost:6060/debug/pprof/heap
   ```

3. **Goroutine Analysis**: Debug goroutine leaks or blocking operations

   ```bash theme={null}
   go tool pprof -http=:8080 http://localhost:6060/debug/pprof/goroutine
   ```

4. **Blocked Operations**:
   ```bash theme={null}
   go tool pprof -http=:8080 http://localhost:6060/debug/pprof/block
   ```

### Considerations

* **Local Only**: The profiling server binds to `localhost:6060` and is not accessible externally
* **Disable in Production**: Only enable profiling for debugging/optimization sessions
* **Performance Impact**: Profiling has minimal overhead but should be used carefully

## Best Practices

1. **Use the Pre-built Dashboard**: Start with the included SigNoz dashboard for immediate visibility
2. **Monitor Both Sides**: Track both pgstream metrics AND source PostgreSQL replication metrics
3. **Watch Runtime Metrics**: Monitor memory usage, GC performance, and goroutine counts for application health
4. **Set Up Comprehensive Alerting**: Configure alerts for both application and infrastructure metrics
5. **Regular Health Checks**: Use the dashboard to perform regular health assessments
6. **Analyze Traces for Debugging**: Leverage distributed tracing for complex issue resolution
7. **Capacity Planning**: Use historical metrics data to plan for scaling needs
8. **Enable Profiling Only When Needed**: Don't run profiling continuously in production
9. **Collect Sufficient Profile Data**: Let profiling run for adequate time to collect meaningful samples

## Troubleshooting

* **High Memory Usage**: Check `runtime.go.mem.heap_alloc` and look for memory leaks in processing pipelines
* **Goroutine Leaks**: Monitor `runtime.go.goroutines` and investigate if count keeps growing
* **GC Pressure**: High `runtime.go.gc.count` rate may indicate memory allocation issues
* **High Lag**: Check both `pgstream.replication.lag` and PostgreSQL `pg_stat_replication` for the root cause
* **Write Failures**: Monitor `pgstream.kafka.writer.latency` and check Kafka connectivity
* **Slow Snapshots**: Use `pgstream.snapshot.generator.latency` with schema/table attributes to identify problematic tables
* **Query Performance**: Analyze `pgstream.postgres.querier.latency` by `query_type` to find slow operations
* **Missing Data**: Check PostgreSQL replication slot status and WAL retention policies

## Getting Help

If you encounter issues with observability setup:

1. Check the SigNoz dashboard for immediate insights
2. Review both pgstream and PostgreSQL metrics
3. Examine Go runtime metrics for application health issues
4. Use profiling to drill down into performance bottlenecks
5. Examine distributed traces for detailed execution flow
6. Consult the troubleshooting section above
7. Check PostgreSQL logs for replication related errors
