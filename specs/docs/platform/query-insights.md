> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Query insights

> Identify slow and expensive queries on your branch with statistics from pg_stat_statements

Query insights surfaces aggregated execution statistics for the queries running on a branch so you can spot slow statements, hot paths, and resource-heavy workloads at a glance. The page reads from PostgreSQL's [`pg_stat_statements`](https://www.postgresql.org/docs/current/pgstatstatements.html) extension and groups statements by their normalized form, regardless of literal values.

The same statistics are available from a terminal or a script with [`xata branch query-insights`](/docs/cli/branch#query-insights). See [From the command line](#from-the-command-line) below.

## Find slow queries

To find the slowest queries on a branch, open **Query insights** and sort by **Avg** to rank statements by mean execution time, or leave the default sort on total execution time to find the statements consuming the most database time overall. Any statement whose slowest recorded execution exceeded one second is highlighted in the **Max** column.

From there, click a row to see the full statement, how many times it ran, and its cache hit ratio. A low cache hit ratio means the query is reading from disk more than expected, which usually points at a missing index or a working set larger than available memory.

To do the same thing from a terminal, run `xata branch query-insights list --performance slow --sort mean-time`.

## Enable the extension

Query insights requires the `pg_stat_statements` extension, which must be added to the preloaded libraries for your branch before it can be used.

1. Open the **Query insights** page for a branch.
2. If the extension is not yet enabled, an **Enable pg\_stat\_statements** prompt is shown — click it to add `pg_stat_statements` to the preloaded libraries and create the extension. A branch restart is required to load the library.
3. Once the branch finishes restarting, return to the Query insights page to start exploring statistics.

You can also enable the extension manually from the [Extensions](/docs/platform/extensions) settings of your branch.

## Queries table

The main view lists every normalized statement that `pg_stat_statements` has captured since it was last reset, sorted by total execution time. Click any row to open an inline detail panel with the full statement and metadata. Use the arrows in the panel header to move to the previous or next query without closing it.

### Filters

Narrow the table using the controls above the headline:

* **Search** — match against the query text, database, or role.
* **Type** — filter by statement type (`SELECT`, `INSERT`, `UPDATE`, `DELETE`, `CREATE`, `ALTER`, `OTHER`).
* **Database** — restrict to one or more databases on the branch.
* **User** — restrict to one or more roles that ran the queries.

The row counter below the headline shows how many queries match the active filters out of the total captured.

### Columns

A column selector in the toolbar lets you toggle which statistics are visible. The **Query** column is always shown.

| Column                                | Description                                               |
| ------------------------------------- | --------------------------------------------------------- |
| Query                                 | Normalized SQL text                                       |
| Database                              | Database the query ran against                            |
| User                                  | Role that executed the query                              |
| Runtime                               | Share of total execution time across all captured queries |
| Calls                                 | Number of times the query was executed                    |
| Total                                 | Cumulative execution time                                 |
| Avg                                   | Mean execution time per call                              |
| Min                                   | Fastest recorded execution                                |
| Max                                   | Slowest recorded execution (highlighted when over 1s)     |
| Stddev                                | Standard deviation of execution time                      |
| Rows                                  | Total rows retrieved or affected                          |
| Shared Hit / Read / Dirtied / Written | Shared buffer activity                                    |
| Local Hit / Read / Dirtied / Written  | Local buffer activity (temp tables)                       |
| Temp Read / Written                   | Temporary file I/O                                        |

By default, the table shows **Query**, **Runtime**, **Calls**, **Total**, **Avg**, **Max**, and **Rows**.

## Query detail panel

Selecting a row opens a resizable detail panel, shown as a bottom sheet on narrow screens, with the full statement and a summary of its execution profile:

* **Query ID** — the `pg_stat_statements` `queryid` (copy with one click).
* **Full SQL** — the normalized statement, formatted with syntax highlighting.
* **Database** and **Role**.
* **Count** — total calls.
* **Max time** and **Total exec time**.
* **Cache hit ratio** — computed from `shared_blks_hit` and `shared_blks_read`. A low ratio suggests the query is going to disk more than expected.

## Refresh and reset statistics

The toolbar exposes two actions:

* **Refresh** — re-runs the underlying query against `pg_stat_statements` to pull the latest counters.
* **Reset** — calls `pg_stat_statements_reset()` to clear all captured statistics. Statistics begin rebuilding as new queries are executed. Resetting requires superuser privileges; if the action is denied, ask an administrator to run it for you.

<Warning>
  Resetting statistics is branch-wide and cannot be undone. All historical query counters are discarded.
</Warning>

## From the command line

[`xata branch query-insights`](/docs/cli/branch#query-insights) (alias `qi`) exposes the same `pg_stat_statements` data outside the dashboard, with `--output json` and `--output ndjson` for scripts and other tools. `list` covers the queries table, `show` covers the detail view, and `enable` and `reset` match the toolbar actions.

The CLI also has one capability the dashboard does not: `xata branch query-insights active` lists the queries executing at this moment, with their age, state, and wait event, read from `pg_stat_activity`. There is no page for this in the dashboard.

Note that `query-insights` opens a direct PostgreSQL connection to the branch, so it requires the `credentials:read` scope on your API key and a healthy branch, meaning one that is not hibernated or still provisioning.

## Availability

Query insights is available on any branch that has `pg_stat_statements` enabled. The page is not available while a branch is hibernated, still provisioning, or has public access disabled. In those cases, the standard branch status view is shown instead. The `active` subcommand in the CLI is the one exception to the extension requirement: it reads `pg_stat_activity`, which is always present.
