> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Bulk Delete Branches

> GitHub Actions workflow for bulk deleting Xata branches with the Xata CLI, jq, and bash

This workflow shows a simple bulk-delete pattern using `xata branch list --json`, `jq`, and `bash`. The example deletes stale `gh-readonly-queue*` branches older than 1 hour, but the same pattern works for any bulk-delete use case — for example, cleaning up branches by naming convention, age, region, or any other attribute exposed by the Xata CLI.

The pattern in this guide is intentionally minimal. It does not require TypeScript, the Xata SDK, or any custom scripts — only the Xata CLI, `jq`, and a short bash loop in a scheduled GitHub Actions workflow.

## Workflow configuration

```yaml theme={null}
name: Bulk delete Xata branches

on:
  schedule:
    - cron: "0 * * * *" # Run every hour
  workflow_dispatch:

permissions:
  contents: read

jobs:
  cleanup:
    name: Delete stale Xata branches
    runs-on: ubuntu-latest
    env:
      XATA_API_KEY: ${{ secrets.XATA_API_KEY }}
      XATA_ORGANIZATION_ID: ${{ vars.XATA_ORGANIZATION_ID }}
      XATA_PROJECT_ID: ${{ vars.XATA_PROJECT_ID }}

    steps:
      - name: Install Xata CLI
        shell: bash
        run: |
          curl -fsSL https://xata.io/install.sh | bash
          echo "/home/runner/.config/xata/bin" >> "$GITHUB_PATH"

      - name: Delete stale Xata branches
        shell: bash
        run: |
          set -euo pipefail

          xata branch list \
            --organization "$XATA_ORGANIZATION_ID" \
            --project "$XATA_PROJECT_ID" \
            --json \
            | jq -r '
                .[]
                | select(.name | startswith("gh-readonly-queue"))
                | select((.createdAt | fromdateiso8601) < (now - 3600))
                | .name
              ' \
            | while IFS= read -r branch; do
                [ -z "$branch" ] && continue
                echo "Deleting Xata branch: $branch"
                xata branch delete \
                  --organization "$XATA_ORGANIZATION_ID" \
                  --project "$XATA_PROJECT_ID" \
                  --yes \
                  "$branch"
              done
```

## Authentication and environment variables

The workflow requires the following environment variables:

* `XATA_API_KEY`: Your Xata API key (set as a GitHub secret).
* `XATA_ORGANIZATION_ID`: Your Xata organization ID.
* `XATA_PROJECT_ID`: Your Xata project ID.

If you have the Xata CLI configured with your project locally, you can obtain these values with:

```sh theme={null}
# XATA_API_KEY
xata keys organization create

# XATA_ORGANIZATION_ID
xata organization get id

# XATA_PROJECT_ID
xata project get id
```

## Workflow steps

### 1. Install the Xata CLI

```yaml theme={null}
- name: Install Xata CLI
  shell: bash
  run: |
    curl -fsSL https://xata.io/install.sh | bash
    echo "/home/runner/.config/xata/bin" >> "$GITHUB_PATH"
```

This step installs the Xata CLI using the official installer script and adds it to the GitHub Actions `PATH`. `jq` is preinstalled on `ubuntu-latest` runners and does not require a separate install step.

### 2. List branches as JSON

```bash theme={null}
xata branch list \
  --organization "$XATA_ORGANIZATION_ID" \
  --project "$XATA_PROJECT_ID" \
  --json
```

The `--json` flag returns the full branch list as a JSON array, including `name`, `id`, `createdAt`, and other metadata. This output is the input to the filter pipeline.

### 3. Filter with jq

```jq theme={null}
.[]
| select(.name | startswith("gh-readonly-queue"))
| select((.createdAt | fromdateiso8601) < (now - 3600))
| .name
```

This filter keeps only branches that:

* Have a name starting with `gh-readonly-queue`.
* Were created more than 1 hour ago (`now - 3600` seconds).

It then emits just the branch name on stdout, one per line.

### 4. Delete with a bash loop

```bash theme={null}
while IFS= read -r branch; do
  [ -z "$branch" ] && continue
  echo "Deleting Xata branch: $branch"
  xata branch delete \
    --organization "$XATA_ORGANIZATION_ID" \
    --project "$XATA_PROJECT_ID" \
    --yes \
    "$branch"
done
```

The loop reads each branch name from stdin and runs `xata branch delete --yes` against it. The `--yes` flag is required in non-interactive environments such as GitHub Actions to skip the confirmation prompt.

## Customizing the filter

The same pipeline works for any bulk-delete rule. Replace the `jq` filter with whatever conditions match your use case:

```bash theme={null}
xata branch list --json \
  | jq -r '
      .[]
      | select(<your jq conditions>)
      | .name
    ' \
  | while IFS= read -r branch; do
      [ -z "$branch" ] && continue
      xata branch delete --yes "$branch"
    done
```

Common variations:

* **Match a different naming convention.** Replace `startswith("gh-readonly-queue")` with `startswith("preview-")`, `test("^pr-[0-9]+$")`, or any regex that matches your branch names.
* **Change the age threshold.** Replace `(now - 3600)` with `(now - 86400)` for 1 day, `(now - 604800)` for 1 week, and so on.
* **Exclude protected branches.** Add `select(.name != "main" and .name != "staging")` to make sure important branches are never matched.
* **Combine conditions.** Chain multiple `select(...)` filters to express more specific rules.

## Best practices

1. **Start with a narrow filter.** Begin with a specific name prefix and an age threshold, then broaden the rule once you are confident.
2. **Protect important branches explicitly.** Add `select(.name != "main" and .name != "staging")` to your filter when running broader cleanups.
3. **Dry-run before deleting.** Replace the `xata branch delete` call with `echo "$branch"` and run the workflow manually with `workflow_dispatch` to preview which branches would be deleted.
4. **Schedule conservatively.** Hourly is appropriate for short-lived branches such as merge-queue branches; daily or weekly is usually enough for most cleanup rules.

## When to use

Use this workflow when you want to:

* Automatically remove short-lived branches such as merge-queue or preview branches.
* Enforce branch retention policies based on age or naming.
* Bulk-delete branches matching any rule expressible with the Xata CLI output and `jq`.

For more advanced cleanup rules, you can extend this pattern by fetching external state (for example, the list of open GitHub branches via `gh api`) and deleting Xata branches that no longer have a corresponding upstream branch.

## Related workflows

* [Merge Readiness](./ga-check) — For validating main branch status.
* [Create Branch](./ga-pr) — For managing pull request branches.
* [Branch CLI reference](../cli/branch) — Full reference for `xata branch list` and `xata branch delete`.
