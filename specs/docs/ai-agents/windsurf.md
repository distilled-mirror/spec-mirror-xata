> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Windsurf

> Use Windsurf Cascade and Flows with repeatable Xata branch workflows.

# Windsurf with Xata

Windsurf's Cascade and Flows can handle multi-step coding tasks. Pair them with Xata branches so every flow has a safe Postgres target for migrations, tests, and database inspection.

## What Windsurf is good for with Xata

* Turning repeated database setup into a Flow.
* Making multi-file application and schema changes in one IDE session.
* Running terminal commands while preserving project context.
* Debugging application behavior against a realistic branch.

## Quick start

1. Create a branch for the Windsurf task.

```bash theme={null}
xata branch create --name windsurf-task --parent-branch `xata branch get id`
```

2. Store the branch URL where the app reads local environment variables.

```bash theme={null}
echo "DATABASE_URL=$(xata branch url windsurf-task)" >> .env.local
```

3. Open the project in Windsurf.
4. Ask Cascade to use the local branch for all database commands.

## Recommended Xata setup

Create a Flow for the database setup you repeat often: create branch, print branch URL, run migrations, run tests, and report branch status. Keep destructive operations behind explicit approval.

## Example prompts

* "Create a Flow that prepares a Xata branch, runs migrations, runs tests, and reports results."
* "Use the Xata branch URL in `.env.local`; do not use production credentials."
* "Apply this schema change to the branch and explain the SQL."
* "Debug the failing test by inspecting the branch schema and application logs."
* "Add cleanup instructions for the temporary branch."

## Common workflows

### Flow-based branch setup

1. Create a Flow with Xata CLI setup commands.
2. Add project migration and test commands.
3. Ask Cascade to run the Flow before implementation.
4. Review the Flow output before merging changes.

### Migration testing

1. Create a branch from the current parent.
2. Ask Cascade to apply the migration.
3. Run application tests against the branch.
4. Ask Cascade to summarize changed tables and columns.

## Tips

* Keep the branch name visible in the Flow output.
* Ask Cascade to pause before deleting branches or changing credentials.
* Use anonymized clones for flows that need production-like data.
* Keep `.env.local` uncommitted.

## Troubleshooting

| Issue                                 | Fix                                                                        |
| ------------------------------------- | -------------------------------------------------------------------------- |
| Cascade forgets the database target   | Put the branch name and environment file path in the Flow description.     |
| A Flow runs against stale credentials | Regenerate the branch URL and restart the local dev server.                |
| Generated SQL is too broad            | Ask Cascade to split schema and data changes into separate reviewed steps. |

## Related Xata docs

* [Instant Branching](/docs/core-concepts/branching)
* [Data Anonymization](/docs/core-concepts/anonymization)
* [Schema Changes](/docs/core-concepts/schema-changes)
* [Branch commands](/docs/cli/branch)
