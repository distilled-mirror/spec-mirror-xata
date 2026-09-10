> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Claude Code

> Use Claude Code with isolated Xata branches and branch-scoped Postgres connection strings.

# Claude Code with Xata

Claude Code is a terminal-based coding agent. With Xata, it can work against a dedicated Postgres branch while it edits files, runs tests, and checks schema behavior.

## What Claude Code is good for with Xata

* Creating feature branches that include code and database changes.
* Running migrations against a temporary Postgres branch before review.
* Inspecting schema, connection errors, and test failures from the terminal.
* Producing a summary of database changes before a pull request is merged.

## Quick start

1. Install and authenticate the Xata CLI.

```bash theme={null}
curl -fsSL https://xata.io/install.sh | bash
xata auth login
xata init
```

2. Create a branch for Claude Code.

```bash theme={null}
xata branch create --name claude-task --parent-branch `xata branch get id`
```

3. Store the branch connection string in your local environment.

```bash theme={null}
echo "DATABASE_URL=$(xata branch url claude-task)" >> .env
```

4. Start Claude Code from the project directory and describe the task.

```bash theme={null}
claude
```

## Recommended Xata setup

Use a branch name that matches the task or pull request. If the task needs realistic data, create the branch from an anonymized clone rather than the production branch. Give Claude Code only the connection string for the branch it should use.

For schema work, ask Claude Code to run migrations against the branch and report the migration files, SQL statements, and test output before you apply anything to production.

## Example prompts

* "Use the Xata branch in `DATABASE_URL` for all database work. Do not connect to production."
* "Create a migration for this feature, run it against the current Xata branch, and show me the schema diff."
* "Run the test suite against this branch and fix any application code that assumes the old schema."
* "Inspect the failing query and explain whether the issue is SQL, connection configuration, or missing data."
* "Summarize every database change you made before I open a pull request."

## Common workflows

### Branch-scoped feature work

1. Create a Xata branch for the task.
2. Store `xata branch url <branch>` in `.env`.
3. Ask Claude Code to implement the feature and run tests.
4. Review code, migration files, and branch state.
5. Delete the branch after merge or when the task is abandoned.

### Migration review

1. Ask Claude Code to generate or update the migration.
2. Run the migration against the task branch.
3. Ask Claude Code to verify reads and writes against the new schema.
4. Review the SQL and test output before applying the migration elsewhere.

## Tips

* Keep production credentials out of files the agent can edit.
* Prefer one Xata branch per task so failures are easy to reset.
* Use [scale to zero](/docs/core-concepts/scale-to-zero) for idle agent branches.
* Link Claude Code to existing docs by mentioning [branch commands](/docs/cli/branch) and [schema changes](/docs/core-concepts/schema-changes) in prompts.

## Troubleshooting

| Issue                                      | Fix                                                                                                                        |
| ------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------- |
| Claude Code connects to the wrong database | Replace `DATABASE_URL` with `xata branch url <branch>` and ask it to print the active database host before running writes. |
| Migrations pass locally but fail in CI     | Create the CI branch from the same parent and run the same migration command there.                                        |
| The agent changed production-like data     | Stop the task, rotate exposed credentials if needed, and repeat the work on an isolated branch.                            |

## Related Xata docs

* [Quickstart](/docs/quickstart)
* [CLI branch commands](/docs/cli/branch)
* [Instant Branching](/docs/core-concepts/branching)
* [Schema Changes](/docs/core-concepts/schema-changes)
* [API Keys](/docs/platform/api-key)
