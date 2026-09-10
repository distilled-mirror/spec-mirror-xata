> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# GitHub Copilot

> Use GitHub Copilot with Xata per-PR branches and CI-backed database workflows.

# GitHub Copilot with Xata

GitHub Copilot can help write application code, migrations, and CI configuration. Xata is a good fit for Copilot agent workflows when every pull request gets its own Postgres branch.

## What GitHub Copilot is good for with Xata

* Generating GitHub Actions workflows for database-backed pull requests.
* Updating code and migrations inside VS Code.
* Explaining test failures from CI logs.
* Producing pull request summaries that include database changes.

## Quick start

1. Create a Xata API key for automation.
2. Add the key to GitHub repository secrets as `XATA_API_KEY`.
3. Use the [Create Branch workflow](/docs/automations/ga-pr) as the starting point for per-PR branches.
4. Ask Copilot to adapt the workflow to your repository's package manager, test command, and migration command.

## Recommended Xata setup

Use branch-per-PR automation for team workflows. Copilot can update the workflow file, but keep secrets in GitHub and branch credentials scoped to the pull request. For local VS Code work, use a local `.env` or `.env.local` file with a branch URL.

## Example prompts

* "Create a GitHub Actions workflow based on Xata's per-PR branch docs."
* "Use `XATA_API_KEY` from GitHub secrets and do not hardcode credentials."
* "Add a CI step that runs migrations against the Xata PR branch before tests."
* "Explain this failed migration log and suggest the smallest code change."
* "Write a pull request summary that calls out schema and data changes."

## Common workflows

### Per-PR branch automation

1. Add `XATA_API_KEY` to repository secrets.
2. Add the Xata branch workflow from the docs.
3. Ask Copilot to wire the branch URL into your test command.
4. Run migrations and tests in CI.
5. Clean up PR branches after merge.

### Local VS Code changes

1. Create a Xata branch for the task.
2. Store the branch URL in `.env.local`.
3. Ask Copilot to update code and migrations.
4. Run the app and tests locally before pushing.

## Tips

* Ask Copilot to cite the exact Xata docs section it followed.
* Keep GitHub secrets out of generated files.
* Review generated workflow permissions carefully.
* Use [branch cleanup](/docs/automations/ga-cleanup) for repositories with many agent-created pull requests.

## Troubleshooting

| Issue                                               | Fix                                                                                        |
| --------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| GitHub Actions cannot authenticate                  | Confirm `XATA_API_KEY` is present in repository secrets and the key has branch access.     |
| Copilot generates a workflow with broad permissions | Reduce permissions to the minimum needed for the PR workflow before merging.               |
| CI tests use production data                        | Make the branch URL an explicit CI environment variable and fail the job if it is missing. |

## Related Xata docs

* [Create Branch workflow](/docs/automations/ga-pr)
* [Branch cleanup](/docs/automations/ga-cleanup)
* [API Keys](/docs/platform/api-key)
* [CLI environment variables](/docs/cli)
