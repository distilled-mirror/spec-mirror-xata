> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# OpenCode

> Use OpenCode with standard Postgres URLs and Xata CLI workflows.

# OpenCode with Xata

OpenCode is an open-source terminal coding agent. Because Xata exposes standard Postgres connection strings, OpenCode can work with Xata through the same environment variables, drivers, and CLI commands your application already uses.

## What OpenCode is good for with Xata

* Local terminal loops where you choose the model and provider.
* Database-aware code changes that use standard Postgres tooling.
* Reproducible branch setup in shell scripts.
* Provider-agnostic experimentation against temporary Xata branches.

## Quick start

1. Install and initialize the Xata CLI.

```bash theme={null}
curl -fsSL https://xata.io/install.sh | bash
xata auth login
xata init
```

2. Create a branch for the OpenCode session.

```bash theme={null}
xata branch create --name opencode-task --parent-branch `xata branch get id`
```

3. Export the Postgres URL.

```bash theme={null}
export DATABASE_URL="$(xata branch url opencode-task)"
```

4. Start OpenCode from the project directory.

```bash theme={null}
opencode
```

## Recommended Xata setup

Keep OpenCode pointed at a single temporary branch. Use the branch connection string for application tests and standard tools like `psql`, migration CLIs, and ORM generators.

## Example prompts

* "Use the database in `DATABASE_URL`; it is a temporary Xata branch."
* "Run the migration and test commands against this branch only."
* "If you need a fresh database state, ask me before deleting or recreating the branch."
* "Use standard Postgres SQL and avoid Xata-specific assumptions unless the docs say otherwise."
* "Summarize all database writes before finishing."

## Common workflows

### Provider-agnostic database testing

1. Create a branch for the task.
2. Export `DATABASE_URL`.
3. Ask OpenCode to implement the change.
4. Run migrations and tests through the project scripts.
5. Delete the branch when the experiment is done.

### SQL inspection

1. Connect with `psql $(xata branch url opencode-task)`.
2. Ask OpenCode to inspect the schema and failing query.
3. Apply fixes in code or migrations.
4. Re-run the query and tests against the same branch.

## Tips

* Keep the session scoped to a single branch.
* Use `xata branch list` and `xata branch describe` when you need branch state.
* Prefer pooled URLs for high-concurrency local test runners.
* Use [API keys](/docs/platform/api-key) only when the agent needs Xata API access.

## Troubleshooting

| Issue                              | Fix                                                                                     |
| ---------------------------------- | --------------------------------------------------------------------------------------- |
| OpenCode cannot find Xata commands | Confirm the Xata CLI install path is in `PATH`.                                         |
| Tests use a cached production URL  | Print `DATABASE_URL` in the shell before starting the agent and restart the dev server. |
| The branch gets messy              | Delete it with `xata branch delete <name>` and create a fresh branch from the parent.   |

## Related Xata docs

* [CLI Reference](/docs/cli)
* [Branch commands](/docs/cli/branch)
* [Postgres extensions](/docs/platform/extensions)
* [Scale to Zero](/docs/core-concepts/scale-to-zero)
