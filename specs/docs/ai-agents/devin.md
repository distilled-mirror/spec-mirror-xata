> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Devin

> Use Devin with temporary Xata branches, scoped credentials, and explicit cleanup.

# Devin with Xata

Devin is designed for longer-running implementation tasks. When a task touches data or migrations, give Devin a temporary Xata branch and scoped credentials so it can work independently without access to production.

## What Devin is good for with Xata

* Longer implementation tasks that need a realistic database.
* Pull requests that include application, migration, and test changes.
* Repeated investigation work where the agent needs to run commands over time.
* Branch cleanup and handoff summaries after the task is complete.

## Quick start

1. Create a branch for the Devin task.

```bash theme={null}
xata branch create --name devin-task --parent-branch `xata branch get id`
```

2. Create or choose credentials scoped to that branch.
3. Give Devin the branch connection string and the task boundary.
4. Ask Devin to include database changes, test output, and cleanup steps in the handoff.

## Recommended Xata setup

Use a fresh branch for every Devin task. For production-like tasks, start from anonymized data. If Devin needs API access, create a branch-scoped key and revoke it after the task.

## Example prompts

* "Use this Xata branch for all database work and do not connect to production."
* "Implement the feature, run migrations against the branch, and include test output in the pull request."
* "If you need new data, create it only in the temporary branch."
* "Before finishing, summarize code changes, schema changes, credentials used, and cleanup steps."
* "After the task is merged, delete the temporary branch and remove scoped credentials."

## Common workflows

### Long-running feature task

1. Create a branch from the correct parent.
2. Give Devin the branch connection string.
3. Ask Devin to run migrations and tests against the branch.
4. Review the pull request and database summary.
5. Delete the branch after merge.

### Investigation task

1. Create a branch that reproduces the issue.
2. Ask Devin to inspect schema, queries, and logs.
3. Keep all inserts and updates inside the branch.
4. Ask Devin to produce a written finding before changing production workflows.

## Tips

* Use scoped API keys for cloud agents.
* Set a clear cleanup expectation in the first prompt.
* Prefer anonymized clones when the task needs realistic data.
* Ask Devin to report the branch name and database host in every handoff.

## Troubleshooting

| Issue                              | Fix                                                                                     |
| ---------------------------------- | --------------------------------------------------------------------------------------- |
| Devin needs long-lived access      | Use a branch-scoped key and rotate or revoke it after the task.                         |
| The task modifies too much data    | Recreate the branch from the parent and narrow the prompt.                              |
| The handoff omits database changes | Ask Devin to list migrations, SQL commands, branch name, and test output before review. |

## Related Xata docs

* [Data Anonymization](/docs/core-concepts/anonymization)
* [API Keys](/docs/platform/api-key)
* [Instant Branching](/docs/core-concepts/branching)
* [Branch commands](/docs/cli/branch)
