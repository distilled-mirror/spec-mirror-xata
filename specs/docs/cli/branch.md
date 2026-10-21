> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# branch

> Create, list, and manage Xata branches

A branch is a running Postgres database. Creating one copies the data of its parent, and the branch checked out in this folder is the one commands act on when no branch is given.

Every command below also takes `-h, --help`.

* [`xata branch list`](#list) — List all branches
* [`xata branch describe`](#describe) — Describe a branch
* [`xata branch create`](#create) — Create a new branch
* [`xata branch delete`](#delete) — Delete a branch
* [`xata branch logs`](#logs) — Retrieve the PostgreSQL logs of a branch
* [`xata branch url`](#url) — Print URL (connection string) for a branch
* [`xata branch checkout`](#checkout) — Check out a branch in this folder
* [`xata branch tree`](#tree) — List all branches as a tree
* [`xata branch get`](#get) — Get a field from a branch description
* [`xata branch metrics`](#metrics) — Show CPU, memory and disk usage for a branch
* [`xata branch set`](#set) — Set a field value for a branch
* [`xata branch rotate-password`](#rotate-password) — Rotate the database password for a branch
* [`xata branch wait-ready`](#wait-ready) — Wait for a branch to be ready
* [`xata branch query-insights`](#query-insights) — Inspect query statistics and active queries for a branch
  * [`xata branch query-insights list`](#query-insights-list) — List historical query statistics for a branch, by total execution time
  * [`xata branch query-insights show`](#query-insights-show) — Show full query statistics for a query ID
  * [`xata branch query-insights active`](#query-insights-active) — List currently running queries for a branch
  * [`xata branch query-insights enable`](#query-insights-enable) — Enable pg\_stat\_statements for query insights on a branch
  * [`xata branch query-insights reset`](#query-insights-reset) — Reset accumulated query statistics for a branch

## list

List all branches

```bash theme={null}
xata branch list [--organization value] [--project value] [--branch value] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**Aliases:** `xata branch ls`

## describe

Describe a branch

```bash theme={null}
xata branch describe [--organization value] [--project value] [--branch value] [--profile value] [--debug] [--json] [<branch>]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**branch** `string`

The branch to describe

**Aliases:** `xata branch view`, `xata branch show`

## create

Create a new branch

A branch is a running Postgres database that starts as a copy of its parent. It takes a moment to come up, so `xata branch wait-ready` is what to run before connecting to it. It is checked out afterwards when this folder already has an organization and a project to work from.

```bash theme={null}
xata branch create [--organization value] [--project value] [--parent-branch value] [--no-parent] [--name value] [--description value] [--instance-type value] [--replicas value] [--region value] [--postgres-version value] [--storage value] [--scale-to-zero true|false] [--inactivity-period value] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--parent-branch** `string`

Parent branch ID or name to fork from. Cannot be combined with --no-parent.

**--no-parent** `boolean` (default: false)

Create a root branch with no parent, instead of forking one

**--name** `string`

Branch name

**--description** `string`

Short description of what the branch is for

**--instance-type** `string`

Instance type for the branch

**--replicas** `string`

Number of read replicas for the branch

**--region** `string`

Region to create the branch in

**--postgres-version** `string`

PostgreSQL version for the branch

**--storage** `string`

Storage in GB for the branch. Root branches only, as a fork inherits its parent

**--scale-to-zero** `true | false`

Scale to zero status for the branch

**--inactivity-period** `string`

Inactivity period for the branch in whole minutes (1–1440)

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**Examples:**

```bash theme={null}
# Branch the current branch
xata branch create --name my-branch
# Branch another branch, by ID or by name
xata branch create --name my-branch --parent-branch main
# Create a root branch with no parent
xata branch create --name my-branch --no-parent
# Create a root branch with a 10 GB disk
xata branch create --name my-branch --no-parent --storage 10
# Describe what the branch is for
xata branch create --name my-branch --description "Nightly import"
# Size the branch and let it scale to zero
xata branch create --name my-branch --instance-type <type> --replicas 1 --scale-to-zero true
```

## delete

Delete a branch

The branch checked out in this folder cannot be deleted, and outside an interactive terminal the confirmation has to come from `--yes`.

> [!WARNING]
>
> Deleting a branch destroys its database and cannot be undone.

```bash theme={null}
xata branch delete [--organization value] [--project value] [--branch value] [--yes] [--profile value] [--debug] [--json] [<branch>]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--yes** `boolean` (default: false)

Do not ask for confirmation, assume yes.

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**branch** `string`

The branch to delete

**Examples:**

```bash theme={null}
# Delete a branch, asking for confirmation
xata branch delete my-branch
# Delete a branch from a script
xata branch delete my-branch --yes
```

## logs

Retrieve the PostgreSQL logs of a branch

Reads the logs of every instance of the branch, the primary and any replicas, which is where slow queries, connection issues and replication problems show up. Requires the `logs:read` scope on the API key. Of the output formats, `raw` prints `<timestamp> [<level> <instanceID> <process>] <message>` per line, `json` a single array, `ndjson` one object per line for streaming into another process, and `csv` the columns `timestamp,level,instanceID,process,message`. Follow mode polls every 2 seconds with a 5 second overlap and de-duplicates entries. Logs can contain connection strings and other credentials, see [https://xata.io/docs/platform/logs](https://xata.io/docs/platform/logs) for what is redacted.

```bash theme={null}
xata branch logs [--organization value] [--project value] [--branch value] [--level debug|info|warning|error] [--instance value]... [--process value]... [--search value] [--start value] [--end value] [--limit value] [--follow] [--output raw|json|ndjson|csv] [--profile value] [--debug] [--json] [<branch>]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--level** `debug | info | warning | error`

Filter by log level. Can be repeated.

**--instance** `string`

Filter by branch instance ID. Can be repeated.

**--process** `string`

Filter by process name. Can be repeated.

**--search** `string`

Case-insensitive substring search in the log message body. For regex filtering, pipe raw output to rg/grep, e.g. xata branch logs --output raw | rg 'timeout|deadlock'

**--start** `string`

Start time as YYYY-MM-DDTHH:mm:ss.sssZ or relative duration, e.g. 15m, 1h, 7d. Defaults to 1h ago.

**--end** `string`

End time as YYYY-MM-DDTHH:mm:ss.sssZ or relative duration. Defaults to now.

**--limit** `string` (default: 100)

Maximum number of logs to fetch, up to 1000

**-f, --follow** `boolean` (default: false)

Poll for new logs continuously. Cannot be combined with --output json

**-o, --output** `raw | json | ndjson | csv`

Output format. Defaults to raw, or json when an AI agent runs the command.

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**branch** `string`

The branch to retrieve logs for

**Examples:**

```bash theme={null}
# Errors from the last 15 minutes
xata branch logs my-branch --level error --start 15m
# Follow errors and pull out the message text
xata branch logs my-branch -f --output ndjson | jq -r 'select(.level=="error") | .message'
# Regex search, which --search does not do, by piping raw output
xata branch logs my-branch --output raw | rg 'timeout|deadlock'
# Export one replica's Postgres process logs to a file
xata branch logs my-branch --process postgres --instance <replica-id> --output csv > logs.csv
```

## url

Print URL (connection string) for a branch

Reads the connection details from the credentials endpoint, so an API key needs the `credentials:read` scope, see [https://xata.io/docs/cli#required-scopes](https://xata.io/docs/cli#required-scopes).

```bash theme={null}
xata branch url [--organization value] [--project value] [--branch value] [--database value] [--type primary|primary-or-replica|replica|pooler] [--profile value] [--debug] [--json] [<branch>]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--database** `string`

Database name

**--type** `primary | primary-or-replica | replica | pooler` (default: primary)

Connection type: primary (direct access to the primary), primary-or-replica (routed access to primary or replicas), replica (read-only access to replicas only, requires at least one replica), pooler (pooled access to the primary, recommended for serverless and high-concurrency workloads)

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**branch** `string`

The branch to get URL for

**Examples:**

```bash theme={null}
# Print the primary connection string
xata branch url main
# Pooled connection string, for serverless workloads
xata branch url main --type pooler
# Read-only connection string that targets replicas
xata branch url main --type replica
```

**Aliases:** `xata branch connection-string`

## checkout

Check out a branch in this folder

Writes the branch to `.xata/` in this folder, so later commands run against it without being told which branch to use.

```bash theme={null}
xata branch checkout [--organization value] [--project value] [--branch value] [--database value] [--profile value] [--debug] [--json] [<branch>]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--database** `string`

Database name

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**branch** `string`

The branch to switch to

**Examples:**

```bash theme={null}
# Check out a branch of the current project
xata branch checkout main
# Check out a branch of another project
xata branch checkout feature-branch --organization org-123 --project proj-456
```

## tree

List all branches as a tree

```bash theme={null}
xata branch tree [--organization value] [--project value] [--branch value] [--show-id] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--show-id** `boolean` (default: false)

Show branch IDs in the tree

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**Aliases:** `xata branch topology`

## get

Get a field from a branch description

Run it without a field to list the fields the description holds.

```bash theme={null}
xata branch get [--organization value] [--project value] [--branch value] [--profile value] [--debug] [--json] <[branch] field>...
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**\[branch] field** `string`

Branch name and/or field to get

**Examples:**

```bash theme={null}
# Read one field of the checked out branch
xata branch get status
# Read one field of another branch
xata branch get my-branch status
```

## metrics

Show CPU, memory and disk usage for a branch

Reports the metrics of every instance of the branch, the primary and any replicas, as a snapshot or continuously with --watch.

```bash theme={null}
xata branch metrics [--organization value] [--project value] [--branch value] [--since value] [--start value] [--end value] [--metrics value] [--instances value] [--aggregations value] [--aggregation avg|max|min] [--refresh value] [--output table|json|ndjson|tui] [--watch] [--profile value] [--debug] [--json] [<branch>]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--since** `string`

Time range ending now, such as 1h, 24h, or 7d

**--start** `string`

Start time as an ISO timestamp

**--end** `string`

End time as an ISO timestamp

**--metrics** `string` (default: default)

Metrics to query: default, all, or a comma-separated list

**--instances** `string` (default: all)

Instances to query: all, primary, replicas, or comma-separated instance IDs

**--aggregations** `string` (default: avg,max,min)

Aggregations to query: comma-separated avg,max,min

**--aggregation** `avg | max | min` (default: avg)

Aggregation to render in table or TUI output

**--refresh** `string` (default: 10s)

Refresh interval for watch mode, such as 10s, 1m, or 500ms

**-o, --output** `table | json | ndjson | tui`

Output format. Defaults to table, or json when an AI agent runs the command.

**-w, --watch** `boolean` (default: false)

Refresh metrics continuously

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**branch** `string`

The branch to show metrics for

**Examples:**

```bash theme={null}
# One-shot snapshot of the default metrics
xata branch metrics my-branch
# Continuously refresh metrics in the TUI
xata branch metrics my-branch --watch
# Stream NDJSON updates every 5 seconds for the primary instance
xata branch metrics my-branch -w --refresh 5s --instances primary --output ndjson
```

## set

Set a field value for a branch

The `postgres-version` field upgrades PostgreSQL, and only accepts compatible upgrades within the same major version and offering type, see [https://xata.io/docs/platform/branch#upgrading-postgresql-versions](https://xata.io/docs/platform/branch#upgrading-postgresql-versions).

```bash theme={null}
xata branch set [--organization value] [--project value] [--branch value] [--profile value] [--debug] [--json] [<field>] [<value>]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**field** `string`

The field to set: name, description, replicas, instance-type, storage, hibernate, scale-to-zero, inactivity-period or postgres-version

**value** `string`

The value to set. Prompted for when omitted in an interactive terminal

**Examples:**

```bash theme={null}
# Set a field non-interactively
xata branch set replicas 2 my-branch
# Describe what the branch is for
xata branch set description "Nightly import"
# Clear the description
xata branch set description ""
# Select the target version interactively
xata branch set postgres-version
# Upgrade to a specific PostgreSQL version
xata branch set postgres-version postgres:17.7
```

## rotate-password

Rotate the database password for a branch

Reads the current username from the credentials endpoint, so an API key needs the `credentials:read` scope, see [https://xata.io/docs/cli#required-scopes](https://xata.io/docs/cli#required-scopes).

```bash theme={null}
xata branch rotate-password [--organization value] [--project value] [--branch value] [--yes] [--profile value] [--debug] [--json] [<branch>]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--yes** `boolean` (default: false)

Do not ask for confirmation, assume yes.

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**branch** `string`

The branch to rotate the password for

## wait-ready

Wait for a branch to be ready

Blocks until the branch is healthy, which is what a script needs after creating one or after a change that restarts it. A hibernated branch stays hibernated unless `--wake` is passed.

```bash theme={null}
xata branch wait-ready [--organization value] [--project value] [--branch value] [--wake] [--profile value] [--debug] [--json] [<branch>]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--wake** `boolean`

Wake up the branch if it is hibernated

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**branch** `string`

The branch to wait for

**Examples:**

```bash theme={null}
# Wait for a branch to come up
xata branch wait-ready my-branch
# Wake a hibernated branch and wait for it
xata branch wait-ready my-branch --wake
```

## query-insights

Inspect query statistics and active queries for a branch

Which statements are slow, which are expensive, and what is running right now. Historical statistics come from `pg_stat_statements`, and the queries running at this moment from `pg_stat_activity`. Running `xata branch qi` without a subcommand is the same as `xata branch qi list`. Except for `active`, which reads `pg_stat_activity`, these commands need `pg_stat_statements` loaded: run `xata branch query-insights enable <branch>` and then wait for the restart with `xata branch wait-ready <branch> --wake`. Unlike `logs` and `metrics`, which go through the API, these commands open a direct PostgreSQL connection: they cannot run against a branch that is hibernated or still provisioning, and an API key needs the `credentials:read` scope. The console reads the same `pg_stat_statements` data, see [https://xata.io/docs/platform/query-insights](https://xata.io/docs/platform/query-insights).

**Aliases:** `xata branch qi`

### query-insights list

List historical query statistics for a branch, by total execution time

In table output a leading `!` marks a row worth a look, a slow mean, spikes far above it, a low cache hit rate, heavy temporary file use or a very large row count, and the command prints the `show` command for each flagged query. Below the table it prints how many queries are being shown and, when more remain, the `list` command that fetches the next page. Both `json` and `ndjson` emit `{ "total", "limit", "offset", "queries" }`, with the statements under `queries`.

```bash theme={null}
xata branch query-insights list [--organization value] [--project value] [--branch value] [--search value] [--type value] [--performance value] [--db value] [--role value] [--sort total-time|mean-time|min-time|max-time|stddev-time|calls|rows|shared-hit|shared-read|shared-dirtied|shared-written|local-hit|local-read|local-dirtied|local-written|temp-read|temp-written|database|user] [--direction asc|desc] [--limit value] [--offset value] [--wide] [--output table|json|ndjson|tui] [--profile value] [--debug] [--json] [<branch>]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--search** `string`

Search query text, database, or role

**--type** `string`

Comma-separated query types: SELECT, INSERT, UPDATE, DELETE, CREATE, ALTER, OTHER

**--performance** `string`

Comma-separated performance categories: fast, moderate, slow

**--db** `string`

Comma-separated database names to include

**--role** `string`

Comma-separated role names to include

**--sort** `total-time | mean-time | min-time | max-time | stddev-time | calls | rows | shared-hit | shared-read | shared-dirtied | shared-written | local-hit | local-read | local-dirtied | local-written | temp-read | temp-written | database | user` (default: total-time)

Sort by a query insight metric or dimension

**--direction** `asc | desc` (default: desc)

Sort direction

**--limit** `string`

Maximum number of rows to return. Defaults to 50 for human output and 1000 for --json.

**--offset** `string` (default: 0)

Number of rows to skip

**-w, --wide** `boolean` (default: false)

Show all pg\_stat\_statements metrics in human output

**-o, --output** `table | json | ndjson | tui`

Output format. Defaults to table, or json when an AI agent runs the command.

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**branch** `string`

The branch to inspect

**Examples:**

```bash theme={null}
# The slowest statements by average execution time
xata branch query-insights list my-branch --performance slow --sort mean-time
# Feed the top 100 statements to another process
xata branch query-insights list my-branch --limit 100 --output ndjson | jq -r '.queries[].query'
```

**Aliases:** `xata branch query-insights ls`

### query-insights show

Show full query statistics for a query ID

A query ID is not unique on its own, the same normalized statement is recorded once per database and role that ran it. When the ID matches more than one row the command exits non-zero, re-run it with `--db` and `--role`. Human output ends with the potential issues found for the query.

```bash theme={null}
xata branch query-insights show [--organization value] [--project value] [--branch value] [--db value] [--role value] [--profile value] [--debug] [--json] <queryid> [<branch>]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--db** `string`

Database name to disambiguate the query ID

**--role** `string`

Role name to disambiguate the query ID

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**queryid** `string` *required*

Query ID from pg\_stat\_statements

**branch** `string`

The branch to inspect

**Examples:**

```bash theme={null}
# Full detail for one query
xata branch query-insights show -6744440887696913970 --db postgres --role postgres my-branch
```

**Aliases:** `xata branch query-insights get`

### query-insights active

List currently running queries for a branch

Reads `pg_stat_activity`, so it works without `pg_stat_statements` being loaded. The columns are PID, Age, State, Wait, DB, User, Client and Query.

```bash theme={null}
xata branch query-insights active [--organization value] [--project value] [--branch value] [--watch value] [--profile value] [--debug] [--json] [<branch>]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--watch** `string`

Refresh interval in seconds. Cannot be combined with --json

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**branch** `string`

The branch to inspect

**Examples:**

```bash theme={null}
# What is running right now, refreshed every 5 seconds
xata branch query-insights active my-branch --watch 5
```

### query-insights enable

Enable pg\_stat\_statements for query insights on a branch

Adds `pg_stat_statements` to the preloaded libraries, which restarts the branch, and creates the extension. Run it, wait for the branch with `xata branch wait-ready <branch> --wake`, then run it again to create the extension. Operations that rebuild a branch, such as a migration into it, can drop the extension, so run this again if query insights stop returning rows. The same can be done from the console, by adding it to the preloaded libraries in the branch settings, see [https://xata.io/docs/platform/extensions](https://xata.io/docs/platform/extensions).

```bash theme={null}
xata branch query-insights enable [--organization value] [--project value] [--branch value] [--profile value] [--debug] [--json] [<branch>]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**branch** `string`

The branch to enable query insights for

### query-insights reset

Reset accumulated query statistics for a branch

Calls `pg_stat_statements_reset()`, which requires elevated privileges.

> [!WARNING]
>
> Resetting is branch-wide and cannot be undone, all historical counters are discarded and rebuild as new queries run.

```bash theme={null}
xata branch query-insights reset [--organization value] [--project value] [--branch value] [--yes] [--profile value] [--debug] [--json] [<branch>]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--yes** `boolean` (default: false)

Do not ask for confirmation, assume yes.

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**branch** `string`

The branch to reset query statistics for


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
