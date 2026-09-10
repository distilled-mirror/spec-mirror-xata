> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Codex

> Use Codex with Xata branches for sandboxed coding sessions and database-aware tests.

# Codex with Xata

Codex can inspect a repository, edit files, run shell commands, and verify changes. Xata gives Codex a safe Postgres branch to use while it implements features or fixes tests.

## What Codex is good for with Xata

* Implementing code changes that need a real Postgres database.
* Running test suites against a branch-scoped connection string.
* Updating SQL, ORM models, or migrations and validating them in one session.
* Cleaning up generated changes before a pull request.

## Quick start

1. Install and authenticate the Xata CLI.

```bash theme={null}
curl -fsSL https://xata.io/install.sh | bash
xata auth login
xata init
```

2. Create a task branch.

```bash theme={null}
xata branch create --name codex-task --parent-branch `xata branch get id`
```

3. Export the branch connection string for the Codex session.

```bash theme={null}
export DATABASE_URL="$(xata branch url codex-task)"
```

4. Start Codex in the repository and include the database boundary in the prompt.

```bash theme={null}
codex
```

## Recommended Xata setup

Use a branch per Codex task. For risky changes, start from anonymized production data so tests cover realistic relationships without exposing sensitive values. If the task needs API access, create a scoped key and remove it after the session.

## Example prompts

* "Use only the Postgres database in `DATABASE_URL`. Do not ask for or use production credentials."
* "Add the feature, update the database migration, and run tests against the Xata branch."
* "Use the Xata CLI docs in `/cli/branch` if you need to inspect branch state."
* "Before finishing, summarize schema changes, test commands, and any rows you inserted."
* "Clean up temporary data in this branch before reporting completion."

## Common workflows

### Branch-per-task implementation

1. Create a Xata branch named for the task.
2. Export `DATABASE_URL` for that branch.
3. Ask Codex to implement the code change.
4. Ask Codex to run migrations and tests.
5. Review the diff and branch state before merging.

### Test failure investigation

1. Reproduce the failing test against the task branch.
2. Ask Codex to inspect the query, schema, and fixtures.
3. Let Codex change application code or test setup.
4. Keep data changes inside the temporary branch.

## Tips

* Put database boundaries in the first prompt.
* Prefer branch-scoped API keys for automation work.
* Ask Codex to show exact commands before destructive database operations.
* Use [branch cleanup automation](/docs/automations/ga-cleanup) for repeated team workflows.

## Troubleshooting

| Issue                               | Fix                                                                                           |
| ----------------------------------- | --------------------------------------------------------------------------------------------- |
| Codex cannot connect to Postgres    | Re-run `xata branch url codex-task` and confirm `DATABASE_URL` is exported in the same shell. |
| Codex proposes production writes    | Reject the step and restate that all writes must target the Xata task branch.                 |
| The task creates noisy fixture data | Ask Codex to delete test rows or recreate the branch from its parent.                         |

## Related Xata docs

* [Quickstart](/docs/quickstart)
* [CLI Reference](/docs/cli)
* [Branch commands](/docs/cli/branch)
* [Data Anonymization](/docs/core-concepts/anonymization)
* [Create Branch automation](/docs/automations/ga-pr)
