> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Agent onboarding

> A guide for coding agents to help users get started with Xata, from installing the CLI to a working Postgres branch connected to their app.

This page contains instructions for how to help a user get started with Xata. It is written for coding agents; if you are a human, paste the prompt from the [Quickstart](/docs/quickstart) into your agent and it will follow this guide for you.

Walk through it interactively with the user, answer any questions, and make sure they understand what Xata is and what it can do. The end goal is a working Postgres branch connected to their project, plus hands-on experience with the branching workflow that makes Xata different. If they want to migrate an existing database, help them with that as well (see the migration appendix below).

Note that Xata is designed to work for both very technical and non-technical users. Establish their level of technical knowledge at the start and adjust the tone of your questions accordingly. If the user is non-technical, don't ask technical questions about Postgres versions, regions, or instance sizes. Pick good defaults and explain what you are doing in plain language.

Ground rules for this onboarding:

* Run the commands for the user, but explain why you are running them so they can follow along.
* The Xata CLI detects coding agents and disables interactive prompts. Always pass explicit flags, and add `--json` when you need to parse output. When a command fails because a value is missing or invalid, the error names the flag to pass — ask the user for that value rather than guessing.
* Before starting, look at the current directory. If there is an existing application, ask whether to connect it to Xata; if not, ask whether they want to scaffold a small app or just explore the database workflow. Don't scaffold a project without agreement.
* Ask before installing software, creating billable cloud resources, deleting branches, or copying data from an existing database.
* Never print full connection strings or API keys into the conversation; write them to env files instead.

## What is Xata?

Xata is a PostgreSQL platform built around instant, copy-on-write branching. It is standard PostgreSQL, not a fork: existing drivers, ORMs, and Postgres tools work normally, and there is no SDK to install. (Available extensions are listed at [https://xata.io/docs/platform/extensions](https://xata.io/docs/platform/extensions).)

Key capabilities to explain to the user:

* **Instant branching** — a branch is a full Postgres database that starts as a copy-on-write copy of its parent, without copying the entire dataset. Copy-on-write avoids duplicating inherited data; additional storage and snapshots are billed according to your plan.
* **Scale to zero** — when scale-to-zero is enabled, idle branches release their compute and wake on the next connection, reducing compute costs for per-developer, per-PR, and per-agent branches. Manually hibernated branches without scale-to-zero enabled must be explicitly woken.
* **Data anonymization** — production data can be cloned into branches with PII masked, so development and agent work happens on realistic but safe data.

## Getting started

### Step 1: Install the CLI

The CLI is the main way you (the agent) and the user will interact with Xata. Confirm with the user, then run the install:

```bash theme={null}
# macOS / Linux
curl -fsSL https://xata.io/install.sh | bash

# Windows
powershell -c "irm https://xata.io/install.ps1 | iex"
```

The binary installs to `~/.config/xata/bin`. Make sure that directory is on the PATH, then verify with `xata version`.

### Step 2: Log in

Check whether the user is already authenticated:

```bash theme={null}
xata auth status
```

If not, run `xata auth login`. It prints a URL and a code, then waits. Tell the user to open the URL in their browser, enter the code, and sign in (they can create a free account during this flow with GitHub, Google, or email). Keep the command running until it confirms success.

In headless or CI environments, authenticate with an API key from the environment instead: set `XATA_API_KEY` (keys are created at [https://console.xata.io](https://console.xata.io) or with `xata keys user create`). Prefer a scoped key stored in a secret manager over pasting keys into commands.

### Step 3: Create a project and branch

A **project** is the top-level container; a **branch** is a running Postgres database inside it. Before creating resources, check whether the folder already has a `.xata/` configuration. If so, inspect `xata status` and ask whether to reuse that setup or connect to a different project. `xata init` does not replace an existing project link just because new flags are supplied. Don't delete existing configuration or create another project without agreement.

Ask whether the user is setting up **development and branching** or a **production database**, then recommend settings in plain language:

* For development-only setups, use `--replicas 0`: one primary with no read replicas, reducing compute cost but providing no standby failover target. For production, default to `--replicas 1`: one primary plus one read replica, which provides a standby failover target and adds compute cost.
* For development-only setups, use `--scale-to-zero-base true` so the base branch can sleep when idle. For production, use `--scale-to-zero-base false` to keep it running. Use `--scale-to-zero-child true` for development branches in either case. When scale-to-zero is enabled, the default inactivity interval is **30 minutes**. Explain that sleeping branches save compute but incur wake-up latency.

Inform the user of the proposed region, instance size, replicas, and sleep behavior without requiring them to choose technical flags. For example: "I'll use one primary and one read replica for a standby failover target. The replica adds compute cost. Since this is production, I'll keep the main branch running and let development branches sleep when idle." Obtain approval for billable resources before provisioning.

For a new setup, find or create an organization, then create the project with an initial `main` branch. For development-only use, replace `<replicas>` with `0` and `<base-scale-to-zero>` with `true`; for production, use `1` and `false`, respectively. Because prompts are disabled for agents, pass explicit flags:

```bash theme={null}
xata organization list --json
# if the user has no organization yet:
xata organization create --name "<user or company name>" --json

xata project create --organization <org-id> --name "<project>" --branch-name main \
  --region <region> --replicas <replicas> --instance-type <instance-type> --postgres-version <version> \
  --scale-to-zero-base <base-scale-to-zero> --scale-to-zero-child true --json
```

Region, instance type, and Postgres version choices depend on the organization and plan. If a value is rejected, the error says so — ask the user what they want, or have them run `xata project create --name "<project>"` in their own terminal, where the CLI lists the valid options interactively. Explain any change to the proposed settings before proceeding.

Wait for the branch to be ready, then link the user's project folder so later commands don't need IDs. The explicit wait also supports CLI versions that return from `init` before an unhealthy branch is ready:

```bash theme={null}
xata branch wait-ready --organization <org-id> --project <project-id> --branch main --wake
xata init --organization <org-id> --project <project-id> --branch main --database postgres
```

`xata init` writes the configuration to `.xata/` in the current folder. `--database` selects the Postgres database on the branch; use the existing `postgres` database for onboarding. Verify with `xata status` that the organization, project, branch, and database match the intended target before changing application configuration or running migrations. A successful exit alone is not proof that the folder was linked to the new project.

### Step 4: Connect the user's application

Get the branch connection string and put it wherever the user's app reads its database URL (commonly `DATABASE_URL`):

```bash theme={null}
xata branch url
```

This is a standard `postgresql://` connection string containing credentials. Before writing it anywhere: check which env file the framework actually loads (`.env.local` for Next.js, `.env` otherwise), make sure that file is gitignored, don't duplicate an existing `DATABASE_URL` entry, and don't echo the URL into the conversation. A safe pattern:

```bash theme={null}
echo "DATABASE_URL=$(xata branch url)" >> .env
```

Because Xata is standard Postgres, the user's existing driver or ORM (Prisma, Drizzle, SQLAlchemy, ActiveRecord, ...) works without changes. Add `--type pooler` for serverless workloads that need pooled connections.

### Step 5: Create the schema and some data

Prefer the project's existing migration tooling (Prisma migrate, Drizzle Kit, etc.) if there is one — point it at the branch and run it. Otherwise apply SQL directly. Check that `psql` is installed (`command -v psql`) before using it; if it's missing, use the ORM or ask before installing anything:

```bash theme={null}
psql "$(xata branch url)" -c "CREATE TABLE items (id SERIAL PRIMARY KEY, name TEXT NOT NULL);"
psql "$(xata branch url)" -c "INSERT INTO items (name) SELECT 'item ' || i FROM generate_series(1, 10) i;"
```

Write the schema the user's project actually needs — don't implement persistence in memory when a real database is one command away. Keep schema migrations in the repo.

Before moving on, verify the connection end to end: run the user's app (or a one-line query through their driver) and confirm it reads from the branch.

### Step 6: Show off branching

This is the part that makes Xata click. Create a development branch as a copy-on-write copy of `main`, including its data. Child branches default to one primary and zero read replicas, even when the parent has replicas. Keep that zero-replica default for development to reduce compute cost; the child has no standby failover target:

```bash theme={null}
xata branch create --name dev --parent-branch main
xata checkout dev
xata branch wait-ready dev --wake
```

Now CLI commands target `dev`: `xata branch url` prints the `dev` connection string. Note that `xata checkout` changes only the CLI context — the application keeps using whatever `DATABASE_URL` is in its env file until you update it.

Prove the isolation to the user: query `dev` and show the data copied from `main` is already there, insert a row on `dev`, then query `main` and show it is unchanged. Explain the workflow this enables: a branch per feature, per pull request, per teammate, or per coding agent — each isolated, each disposable. Clean up experiments with `xata branch delete <name>` (ask the user before deleting anything).

Switch back with `xata checkout main` when done.

### Step 7: The console

Tell the user about [https://console.xata.io](https://console.xata.io), where they can see their branch tree, metrics, query insights, schema, and manage API keys and team members. The CLI and console are two views of the same platform: agents mostly use the CLI, humans often prefer the console.

## Ongoing agent access

Set the user up so their coding agents keep working well with Xata after onboarding:

* **MCP server** — Xata hosts an MCP server at `https://api.xata.tech/mcp` (Streamable HTTP, OAuth or API-key auth). Offer to configure it in the user's client now, for example `claude mcp add --transport http xata https://api.xata.tech/mcp` for Claude Code, or an entry in `.cursor/mcp.json` for Cursor. Per-client instructions: [https://xata.io/docs/platform/mcp](https://xata.io/docs/platform/mcp).
* **Agent authentication** — a machine-readable guide to obtaining Xata API credentials as an agent: [https://xata.io/auth.md](https://xata.io/auth.md).
* **Claude Code skill** — [https://xata.io/xata-claude-skill/SKILL.md](https://xata.io/xata-claude-skill/SKILL.md) teaches Claude Code to create isolated branches per task.
* **Per-agent branches** — recommend giving every agent task its own branch created from `main` (or an anonymized clone of production) and reviewing changes before they reach `main`. Details: [https://xata.io/docs/ai-agents/overview](https://xata.io/docs/ai-agents/overview).

## Appendix: migrating an existing database

If the user has an existing Postgres database (RDS, Aurora, Cloud SQL, Neon, Supabase, self-hosted, ...), `xata clone` snapshots it into a Xata branch. Treat this as a sensitive operation: get explicit approval before connecting to their database, and never paste its connection string into the conversation — have the user put it in the environment.

```bash theme={null}
export XATA_CLI_SOURCE_POSTGRES_URL="postgresql://user:pass@host:5432/db"   # user provides this out of band
xata clone config --mode auto --validation-mode strict
# Review .xata/clone.yaml and replace sensitive-column noop rules with masking rules.
# Get the user's approval for the reviewed rules and target branch before copying:
xata clone start --validation-mode strict
```

Strict validation requires every table and column to be covered by the rules in `.xata/clone.yaml`; it does **not** guarantee anonymization. Auto-generated `noop` rules copy values unchanged. Identify sensitive columns and replace their `noop` rules with appropriate masking transformations before copying any data. Only use `--validation-mode relaxed` if the user explicitly confirms the data contains nothing sensitive — relaxed mode copies uncovered columns unchanged. Double-check which branch is checked out before starting, so the clone lands where intended.

Provider-specific guides start at [https://xata.io/docs/migrations/aws-rds](https://xata.io/docs/migrations/aws-rds) (sibling pages cover Aurora, Cloud SQL, Azure, Neon, Supabase, and self-hosted). For continuous replication instead of a one-time snapshot, see `xata clone stream`.

## Notes

* It's the coding agent's job to run CLI commands and write schema migrations, not the human user. The human can use [https://console.xata.io](https://console.xata.io) to see everything you did.
* Every page on [https://xata.io](https://xata.io) is available as markdown by appending `.md` to its URL. The docs index for agents is at [https://xata.io/docs/llms.txt](https://xata.io/docs/llms.txt).
* If something fails, `xata status`, `xata auth status`, and `xata branch describe` are the fastest ways to diagnose state.
