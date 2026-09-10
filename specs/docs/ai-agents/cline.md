> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Cline

> Use Cline with approval-based Xata branch workflows in VS Code.

# Cline with Xata

Cline is useful when you want an agent to propose file edits and terminal commands, then wait for approval. That approval model pairs well with Xata branches because each database action can be checked before it runs.

## What Cline is good for with Xata

* Supervised database setup in VS Code.
* Reviewing terminal commands before they touch a branch.
* Browser- or terminal-assisted debugging with a safe database target.
* Step-by-step migration work where every command is approved.

## Quick start

1. Create a Xata branch for the task.

```bash theme={null}
xata branch create --name cline-task --parent-branch `xata branch get id`
```

2. Add the branch URL to local environment configuration.

```bash theme={null}
echo "DATABASE_URL=$(xata branch url cline-task)" >> .env.local
```

3. Open the project in VS Code.
4. Ask Cline to propose commands before running migrations or writes.

## Recommended Xata setup

Use Cline's approval step for every database command. Give it a branch URL, not production credentials. For schema changes, approve the migration generation separately from the migration execution.

## Example prompts

* "Before running any database command, show me the exact command and wait for approval."
* "Use only the Xata branch in `.env.local`."
* "Generate the migration first, then wait before applying it."
* "Inspect this query failure and suggest a fix without changing production data."
* "After the task, list every Xata command you ran."

## Common workflows

### Supervised migration

1. Ask Cline to generate a migration.
2. Review the file diff.
3. Approve applying it to the Xata branch.
4. Approve running tests.
5. Review the summary before merging.

### Branch-scoped debugging

1. Reproduce the issue against the task branch.
2. Ask Cline to inspect logs, SQL, and schema.
3. Approve only the smallest proposed change.
4. Re-run the test against the same branch.

## Tips

* Approve reads freely, but review writes carefully.
* Keep production secrets out of VS Code workspace files.
* Ask Cline to use [branch commands](/docs/cli/branch) for branch state.
* Delete the task branch after the pull request is merged.

## Troubleshooting

| Issue                                            | Fix                                                                          |
| ------------------------------------------------ | ---------------------------------------------------------------------------- |
| Cline proposes a command against production      | Reject it and provide the branch URL explicitly.                             |
| Too many commands are proposed at once           | Ask Cline to split the task into setup, migration, test, and cleanup phases. |
| A generated migration includes unrelated changes | Ask Cline to regenerate it from the branch state and explain each operation. |

## Related Xata docs

* [Quickstart](/docs/quickstart)
* [Branch commands](/docs/cli/branch)
* [Schema Changes](/docs/core-concepts/schema-changes)
* [API Keys](/docs/platform/api-key)
