> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Logs

> Inspect PostgreSQL logs from your branch instances

Branch logs let you inspect PostgreSQL server output across the primary and replicas of a branch. Logs are useful for debugging slow queries, connection issues, replication problems, and other runtime behavior.

Branch logs are generally available on every branch — no opt-in or feature flag is required.

## Viewing logs in the dashboard

Open a branch in the Xata dashboard and select **Logs** in the branch sidebar to browse entries in a searchable viewer.

From the Logs page you can:

* Pick a preset or custom time range.
* Filter by **level** (`error`, `warning`, `info`, `debug`), **instance** (primary or a specific replica), and **process**.
* Search log message bodies with substring or regex matching.
* Click any entry to open a details panel with the full message and metadata.
* Use **Refresh** to fetch the latest entries for the current time range.

To read logs from a terminal or a script, use [`xata branch logs`](/docs/cli/branch#logs), which offers the same filters plus follow mode and `raw`, `json`, `ndjson`, and `csv` output. For anything else programmatic, such as shipping logs to an external observability tool, use the API described below.

## Retrieving logs

Use the `branchLogs` endpoint to fetch logs for a time range. The endpoint requires the `logs:read` scope on your API key.

```bash theme={null}
curl -X POST "https://api.xata.tech/organizations/{organizationId}/projects/{projectId}/branches/{branchId}/logs" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "start": "2026-06-09T00:00:00Z",
    "end":   "2026-06-09T01:00:00Z",
    "limit": 100
  }'
```

The response contains a `logs` array of entries and a `nextCursor` that you can pass back in a follow-up request to page through additional results.

```json theme={null}
{
  "start": "2026-06-09T00:00:00Z",
  "end": "2026-06-09T01:00:00Z",
  "logs": [
    {
      "timestamp": "2026-06-09T00:00:01.234Z",
      "instanceID": "branch-xxx-1",
      "level": "info",
      "process": "postgres",
      "message": "database system is ready to accept connections"
    }
  ],
  "nextCursor": null
}
```

## Request parameters

| Field     | Type                | Description                                                                                                                                                                                                                            |
| --------- | ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `start`   | `string` (RFC 3339) | Start of the time range. Required.                                                                                                                                                                                                     |
| `end`     | `string` (RFC 3339) | End of the time range. Required.                                                                                                                                                                                                       |
| `limit`   | `integer`           | Maximum number of entries to return per request. Default `100`, maximum `200`. To retrieve more, page with `cursor` as described below, or use [`xata branch logs --limit`](/docs/cli/branch#logs), which pages for you up to 1000 entries. |
| `cursor`  | `string`            | Pagination cursor returned by a previous response.                                                                                                                                                                                     |
| `filters` | `array`             | Optional filters. Multiple filters are combined with `AND`.                                                                                                                                                                            |

## Log entry fields

| Field        | Description                                            |
| ------------ | ------------------------------------------------------ |
| `timestamp`  | Time the log was emitted.                              |
| `instanceID` | Instance that produced the entry (primary or replica). |
| `level`      | One of `debug`, `info`, `warning`, `error`.            |
| `process`    | Name of the PostgreSQL process that emitted the log.   |
| `message`    | The log message.                                       |

## Filtering

Pass one or more `filters` to narrow results. Each filter applies to a specific `field` with an `op` and either a `value` or `values`:

| Field      | Description                        |
| ---------- | ---------------------------------- |
| `instance` | Filter by instance ID.             |
| `level`    | Filter by log level.               |
| `process`  | Filter by PostgreSQL process name. |
| `body`     | Filter by the log message body.    |

Supported operators:

* `in` — match any value in `values` (use with `instance`, `level`, `process`).
* `contains` / `icontains` — substring match on the log body (case-sensitive / insensitive).
* `regex` / `iregex` — regular-expression match on the log body.

For example, fetch only error-level entries from the primary instance that mention `deadlock`:

```bash theme={null}
curl -X POST "https://api.xata.tech/organizations/{organizationId}/projects/{projectId}/branches/{branchId}/logs" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "start": "2026-06-09T00:00:00Z",
    "end":   "2026-06-09T01:00:00Z",
    "filters": [
      { "field": "level",    "op": "in",        "values": ["error"] },
      { "field": "instance", "op": "in",        "values": ["branch-xxx-1"] },
      { "field": "body",     "op": "icontains", "value":  "deadlock" }
    ]
  }'
```

## Credential redaction

Branch logs are scrubbed before they reach the API response. If a `CREATE ROLE`, `ALTER ROLE`, `CREATE USER`, `ALTER USER`, `CREATE GROUP`, `ALTER GROUP`, or `CREATE/ALTER USER MAPPING` statement appears in a log line, the statement is truncated at the `PASSWORD` keyword and the secret is replaced with `<REDACTED>`. This applies to both single-line and multi-line log records.

For example, a log line like:

```text theme={null}
LOG: statement: ALTER ROLE app_user WITH PASSWORD 'hunter2'
```

is returned as:

```text theme={null}
LOG: statement: ALTER ROLE app_user WITH PASSWORD <REDACTED>
```

Redaction is applied on the read path, so it covers historical entries as well as newly emitted logs. Other `password` mentions outside role and user-mapping statements are left untouched.

## Pagination

When a response includes a non-null `nextCursor`, pass it back as `cursor` in the next request to fetch the following page. The `start`, `end`, `limit`, and `filters` should remain the same across paged requests.

```bash theme={null}
curl -X POST "https://api.xata.tech/organizations/{organizationId}/projects/{projectId}/branches/{branchId}/logs" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "start":  "2026-06-09T00:00:00Z",
    "end":    "2026-06-09T01:00:00Z",
    "cursor": "<nextCursor from previous response>"
  }'
```
