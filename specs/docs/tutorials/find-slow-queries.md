> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Find and fix slow queries on a Xata branch

> Use query insights, branch logs, and instance metrics to find slow PostgreSQL queries, understand why they are slow, and confirm the fix worked.

When a database gets slow, you usually need three things: which statements are expensive, what the instance is doing while they run, and what the server logged. On Xata all three are available for any branch, in the console and from the CLI, so you can work through them in one place.

This guide walks the loop end to end: find the expensive statements, read one query's profile, see what is running right now, correlate against logs and instance metrics, then confirm the fix.

## 1. Prerequisites

* A Xata branch with a workload on it. A [production clone](/docs/tutorials/create-production-clone) is a good place to do this without touching production.
* The [Xata CLI](/docs/cli) installed, if you want to follow the terminal steps:
  ```bash theme={null}
  curl -fsSL https://xata.io/install.sh | bash
  ```
* An API key with the `credentials:read` and `logs:read` scopes. `credentials:read` is what lets the CLI open a database connection for query insights. See [Required scopes](/docs/cli#required-scopes).

## 2. Enable pg\_stat\_statements

Query statistics come from PostgreSQL's [`pg_stat_statements`](https://www.postgresql.org/docs/current/pgstatstatements.html) extension, which has to be in the branch's preloaded libraries before it can collect anything.

From the console, open **Query insights** on the branch and click **Enable pg\_stat\_statements**. From the CLI:

```bash theme={null}
xata branch query-insights enable my-branch
```

Either route restarts the branch, because loading a shared library requires it. Wait for the branch to come back:

```bash theme={null}
xata branch wait-ready my-branch --wake
```

<Note>
  The extension only records statements executed after it was loaded. If you enable it and immediately look, the table will be close to empty. Let real traffic run for a while before drawing conclusions.
</Note>

## 3. Find the expensive statements

Two different questions get confused here, and they have different answers.

**Which statement consumes the most database time overall** is the default sort, on total execution time. This is the one to start with, because a query taking 20ms that runs a million times costs more than a 3-second report that runs nightly.

**Which statement is slowest per call** is a sort on mean execution time. This is what you want when a specific request feels slow.

From the console, open **Query insights** and sort by **Total** or **Avg**. Anything whose slowest recorded run exceeded one second is highlighted in the **Max** column.

From the CLI:

```bash theme={null}
# Biggest consumers of total database time
xata branch query-insights list my-branch

# Slowest per call, narrowed to the statements Xata classifies as slow
xata branch query-insights list my-branch --performance slow --sort mean-time

# Narrow to one database and only writes
xata branch qi list my-branch --db app --type UPDATE,DELETE
```

`qi` is an alias for `query-insights`, and `list` is the default subcommand, so `xata branch qi my-branch` does the same thing as the first command above.

In table output the CLI appends a **Potential issues** block calling out statements with a high mean execution time or a low cache hit rate, each with the exact command to inspect it next.

## 4. Read one query's profile

Take a query ID from the previous step and look at it in detail. In the console, click the row. From the CLI:

```bash theme={null}
xata branch qi show -6744440887696913970 --db app --role app my-branch
```

The `--db` and `--role` flags matter more than they look. A `pg_stat_statements` query ID is only unique within a database and role pair, so the same normalized statement running as two different roles is two separate rows. If the ID you pass matches more than one, the command warns you and exits non-zero rather than guessing.

What to look at, in rough order of usefulness:

* **Mean versus max execution time.** A large gap means the query is usually fine and occasionally terrible, which points at lock contention, a cold cache, or a plan that flips on certain parameter values. A mean close to the max means it is uniformly slow, which is usually the plan itself.
* **Cache hit ratio**, computed from shared buffer hits against reads. A low ratio means the query is going to disk more than it should. That is either a missing index or a working set bigger than available memory, and step 6 tells you which.
* **Calls and rows.** A query returning far more rows than the application needs is often a missing `LIMIT` or an over-broad join, and it is cheaper to fix in the application than in the database.

## 5. See what is running right now

Aggregated statistics tell you what has been expensive. They do not tell you what is happening at this moment, which is what you want when the database is slow *now*.

```bash theme={null}
xata branch qi active my-branch
```

This reads `pg_stat_activity` and lists each running query with its process ID, age, state, and wait event. The wait event is the useful column: a query waiting on a lock is a different problem from one burning CPU, and they have different fixes.

To watch it, refreshing every five seconds:

```bash theme={null}
xata branch qi active my-branch --watch 5
```

<Note>
  `active` is the one subcommand that does not need `pg_stat_statements`, since `pg_stat_activity` is always present. It is also only available from the CLI; there is no page for it in the console.
</Note>

## 6. Correlate with logs and instance metrics

A slow query is sometimes the cause and sometimes the symptom. Before optimizing a statement, check whether the instance was simply saturated while it ran.

```bash theme={null}
# CPU, memory, disk, connections, IOPS, replication lag
xata branch metrics my-branch

# Live view, refreshed every 5 seconds
xata branch metrics my-branch -w --refresh 5s
```

If CPU is pinned or the connection count is at its ceiling, the slow query may be a victim rather than the culprit. If disk read IOPS are high while your query shows a low cache hit ratio, those two facts are the same fact, and an index is the likely fix.

Then read what the server said:

```bash theme={null}
# Errors in the last 15 minutes
xata branch logs my-branch --level error --start 15m

# Lock and timeout trouble specifically
xata branch logs my-branch --output raw | rg 'timeout|deadlock|lock'
```

`--search` does case-insensitive substring matching only. For regex, pipe `--output raw` into `rg` or `grep`, as above.

## 7. Confirm the fix

After you ship an index or a query change, you need a clean baseline, because the accumulated averages include every slow execution from before the fix.

```bash theme={null}
xata branch query-insights reset my-branch
```

This calls `pg_stat_statements_reset()`, discards all counters for the branch, and cannot be undone. Let traffic run again, then re-check the same sort from step 3 and compare mean execution time and cache hit ratio against what you wrote down earlier.

<Warning>
  Reset is branch-wide. Everyone looking at query insights on that branch loses their history too, so on a shared branch it is worth telling people first.
</Warning>

## Running this from a script or an agent

Every command above produces machine-readable output, so the same loop works unattended. `qi list`, `logs`, and `metrics` take `-o`/`--output` with a format: `json`, `ndjson`, and in some cases `csv` or `tui`. The rest of the subcommands used here, `qi show`, `qi active`, `qi enable`, `qi reset`, and `wait-ready`, take a plain `--json` flag instead, and passing `--output` to them is an error.

```bash theme={null}
# The slow statements, as query IDs
xata branch qi list my-branch --performance slow --output json | jq -r '.queries[].queryid'

# Stream errors as they arrive, one JSON object per entry
xata branch logs my-branch -f --output ndjson | jq -r 'select(.level=="error") | .message'

# A metrics sample every 5 seconds, one report per line
xata branch metrics my-branch -w --refresh 5s --output ndjson

# What is running right now, with wait events. Note --json, not --output json
xata branch qi active my-branch --json | jq -r '.queries[] | "\(.pid) \(.wait_event_type // "-")/\(.wait_event // "-") \(.query)"'
```

`ndjson` means something slightly different per command, which is worth knowing before you write a parser. For `logs` it is one object per log entry, so it streams. For `metrics -w` it is one report per refresh, so it also streams. For `qi list` it is the whole result set on a single line, identical in content to `--output json`, with the statements under a `queries` key.

Two behaviors worth knowing if a coding agent is the one running these. The CLI detects when it is running in CI or inside an agent and skips interactive prompts instead of blocking on input. And `xata branch metrics -w` adapts to its caller: an interactive terminal gets a live dashboard, while a pipe gets an NDJSON stream, with no flag change.

## Next steps

* [Query insights](/docs/platform/query-insights) for the console reference
* [Logs](/docs/platform/logs) and [Metrics](/docs/platform/metrics) for what each surface collects
* [`xata branch` CLI reference](/docs/cli/branch#query-insights) for the full flag surface
* [Create a production clone](/docs/tutorials/create-production-clone) to do this work against realistic data without touching production
