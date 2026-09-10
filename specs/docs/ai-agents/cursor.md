> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Cursor

> Use Cursor with Xata branch connection strings for IDE and background-agent workflows.

# Cursor with Xata

Cursor works well for multi-file edits, background agents, and database-aware application changes. Use a Xata branch as the database target for Composer and agent sessions.

## What Cursor is good for with Xata

* Updating ORM models, queries, and migrations together.
* Testing a feature against an isolated branch from inside the IDE terminal.
* Reviewing generated code and SQL before it leaves your editor.
* Running background agents without giving them production database credentials.

## Quick start

1. Create a Xata branch for the task.

```bash theme={null}
xata branch create --name cursor-task --parent-branch `xata branch get id`
```

2. Put the branch URL in `.env.local`.

```bash theme={null}
echo "DATABASE_URL=$(xata branch url cursor-task)" >> .env.local
```

3. Open the project in Cursor.
4. Ask Composer or a background agent to use the connection string from `.env.local`.

## Recommended Xata setup

Keep a separate branch for each Cursor task or background agent. If Cursor needs to generate migrations, ask it to apply them only to the task branch and show the SQL or migration file before you merge.

## Example prompts

* "Use the Xata branch connection string in `.env.local` for all database work."
* "Update the Drizzle or Prisma schema and run the app against this branch."
* "Generate the migration, apply it to the branch, and show me the SQL before I merge."
* "Create a pull request summary that includes database changes and test output."
* "Do not edit production environment variables."

## Common workflows

### Composer with a branch URL

1. Create a branch for the feature.
2. Add the branch connection string to `.env.local`.
3. Ask Composer to implement the feature.
4. Run the app and tests from Cursor's terminal.
5. Review generated schema and data changes.

### Background agent for pull requests

1. Create a task branch in Xata.
2. Include the branch URL in the agent instructions.
3. Ask the agent to keep database changes isolated.
4. Review every migration and environment change before merging.

## Tips

* Keep `.env.local` out of commits.
* Prefer branch names that match the issue or pull request.
* Ask Cursor to explain generated SQL in plain language.
* Link Cursor to [Instant Branching](/docs/core-concepts/branching) when prompting for database safety.

## Troubleshooting

| Issue                                   | Fix                                                                                                 |
| --------------------------------------- | --------------------------------------------------------------------------------------------------- |
| Cursor edits the wrong environment file | Move production variables out of editable local files and keep only the branch URL in `.env.local`. |
| Background agent loses database context | Add the branch name and `DATABASE_URL` source to the task prompt.                                   |
| Generated migrations are hard to review | Ask Cursor to summarize each operation and run it against the branch before merge.                  |

## Related Xata docs

* [Drizzle quickstart](/docs/quickstarts/drizzle)
* [Instant Branching](/docs/core-concepts/branching)
* [Schema Changes](/docs/core-concepts/schema-changes)
* [Branch commands](/docs/cli/branch)
