> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Metrics

> Monitor your database performance across instances with real-time metrics

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/platform/metrics.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=78f87f8de9dfa303b23c72d1544e23aa" alt="Metrics dashboard showing database performance monitoring with CPU, memory, and disk usage charts" className="rounded-lg" width="2870" height="1550" data-path="images/platform/metrics.png" />

Monitor your database performance across instances with real-time metrics including CPU, memory, disk usage, and more. You can view trends as far back as 30 days with minute level granularity.

The same metrics are available from a terminal with [`xata branch metrics`](/docs/cli/branch#metrics), including a live view via `-w` that renders a dashboard in an interactive terminal and streams NDJSON when piped into another process.

## Aggregations

Xata provides various aggregation methods to help you analyze your metrics data effectively.

* **Average**: Mean values over time periods
* **Min/Max**: Peak and valley values
* **Percentiles**: P50, P95, P99 for performance analysis `Coming Soon`

## Metric Definitions

By default, all metrics have a 1 minute granularity and a retention period of 30 days.

| Metric           | Description                                                                                    | Unit         |
| ---------------- | ---------------------------------------------------------------------------------------------- | ------------ |
| CPU Usage        | Shows the cumulative usage by combining all available cores (e.g. 200% = two fully used cores) | Percentage   |
| Memory Usage     | Shows the amount of memory being used by your application                                      | Bytes        |
| Disk Usage       | Shows the amount of disk being used by your application                                        | Bytes        |
| Connection Count | Shows the number of active and idle client connections to your database                        | Count        |
| Read Throughput  | Shows the amount of data read in bytes                                                         | Bytes        |
| Write Throughput | Shows the amount of data written in bytes                                                      | Bytes        |
| Network Ingress  | Shows the amount of incoming network traffic to your database instances                        | Bytes        |
| Network Egress   | Shows the amount of outgoing network traffic from your database instances                      | Bytes        |
| Read Latency     | Shows the time it takes to complete read operations in milliseconds                            | Milliseconds |
| Write Latency    | Shows the time it takes to complete write operations in milliseconds                           | Milliseconds |
| Read IOPS        | Shows the number of read operations performed by your database instances                       | Count        |
| Write IOPS       | Shows the number of write operations performed by your database instances                      | Count        |
| Wal Sync Time    | Shows the time it takes to synchronize Write-Ahead Log (WAL) data to disk in milliseconds      | Milliseconds |
| Replication Lag  | Shows the time it takes for the replica to catch up with the primary                           | Seconds      |
