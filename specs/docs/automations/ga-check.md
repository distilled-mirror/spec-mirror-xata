> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Merge Readiness

> GitHub Actions workflow for validating main branch status and migration readiness

This workflow validates that the main branch is in a ready state for merging pull requests. It checks the migration status and ensures no pending migrations are running that could affect the merge process.

## Workflow configuration

```yaml theme={null}
name: Check merge readiness

on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  check:
    runs-on: ubuntu-latest
    name: Xata dev branch
    steps:
      - uses: actions/checkout@v3

      - name: Install Xata CLI
        run: |
          curl -fsSL https://xata.io/install.sh | bash

      - name: Check Xata status
        run: |
          xata status
          xata roll status
          xata branch view

      - name: Check Xata main branch pgroll status
        id: xata-main-branch-roll-status
        run: |
          xata roll status | jq -r '.status'
          echo XATA_MAIN_BRANCH_ROLL_STATUS=`xata roll status | jq -r '.status'` >> $GITHUB_OUTPUT

      - name: Complete any pending migrations (inherited from main)
        if: ${{ steps.xata-main-branch-roll-status.outputs.XATA_MAIN_BRANCH_ROLL_STATUS == 'In progress' }}
        run: |
          echo "Warning: A migration is currently running on the main branch. Please wait for it to complete before merging this PR."
          exit 1
```

## Authentication and environment variables

The workflow requires several environment variables to be set:

* `XATA_API_KEY`: Your Xata API key (set as a GitHub secret)
* `XATA_ORGANIZATION_ID`: Your Xata organization ID
* `XATA_PROJECT_ID`: Your Xata project ID
* `XATA_BRANCH_ID`: The ID of the main branch
* `XATA_BRANCH_NAME`: The name of the main branch
* `XATA_DATABASE_NAME`: Your database name

If you have the Xata CLI configured with your project locally, you can obtain these values with the following commands:

```sh theme={null}
# XATA_API_KEY
xata keys organization create

# XATA_ORGANIZATION_ID
xata organization get id

# XATA_PROJECT_ID
xata project get id

# XATA_BRANCH_ID
xata branch get id
```

## Workflow steps

### 1. Install Xata CLI

```yaml theme={null}
- name: Install Xata CLI
  run: |
    curl -fsSL https://xata.io/install.sh | bash
```

This step installs the Xata CLI using the official installer script and adds it to the GitHub Actions PATH.

### 2. Check Xata status

```yaml theme={null}
- name: Check Xata status
  run: |
    xata status
    xata roll status
    xata branch view
```

This step performs three checks:

* Verifies CLI and connection status
* Checks for any pending migrations
* Views the current branch status

## Concurrency control

The workflow uses GitHub Actions concurrency to prevent multiple runs from interfering with each other:

```yaml theme={null}
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

This ensures that:

* Only one workflow run per branch is active at a time
* New runs cancel any in-progress runs
* Prevents race conditions during status checks

## Best practices

1. **Set appropriate timeout**
   * The workflow has a 10-minute timeout
   * Adjust based on your maximum migration duration

2. **Use environment variables**
   * Store sensitive information in GitHub secrets
   * Use environment variables for configuration

3. **Handle migration states**
   * Check for in-progress migrations
   * Prevent merging during active migrations
   * Provide clear error messages

## Related workflows

* [Clone workflow](./ga-clone) - For cloning databases in CI/CD
* [PR workflow](./ga-pr) - For managing pull request environments
