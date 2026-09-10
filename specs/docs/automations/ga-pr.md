> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Create Branch

> GitHub Actions workflow for creating and managing development branches for pull requests

This workflow creates a dedicated Xata branch for each pull request, allowing you to test database changes in isolation. It also manages PR comments to provide branch information and status updates.

## Workflow Configuration

```yaml theme={null}
name: Create Xata dev branch

on:
  pull_request:
    ignore-branches:
      - main

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

env:
  FORCE_COLOR: 3
  XATA_API_KEY: ${{ secrets.XATA_API_KEY }}
  XATA_ORGANIZATION_ID: 123xyz
  XATA_PROJECT_ID: prj_i3qsq56h3p3d3crnk7htnc75d0
  XATA_BRANCH_ID: s99l2grl6d3nr1cqcbaaa5piic
  XATA_BRANCH_NAME: main
  XATA_DATABASE_NAME: app

permissions:
  id-token: write
  contents: write
  packages: write
  pages: write
  pull-requests: write
```

## Authentication and Environment Variables

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

## Required Permissions

The workflow requires the following GitHub permissions:

```yaml theme={null}
permissions:
  id-token: write
  contents: write
  packages: write
  pages: write
  pull-requests: write
```

## Workflow Steps

### 1. Find Previous Comment

```yaml theme={null}
- name: Find Previous Comment
  id: find-comment
  uses: peter-evans/find-comment@v1
  with:
    token: ${{ secrets.GITHUB_TOKEN }}
    issue-number: ${{ github.event.pull_request.number }}
    comment-author: "github-actions[bot]"
    body-includes: "Xata Dev Branch"
```

This step searches for any existing comments from the workflow to update them later.

### 2. Checkout Code

```yaml theme={null}
- uses: actions/checkout@v4
  with:
    fetch-depth: 0
    persist-credentials: false
```

This step checks out your repository code with full history and proper credentials configuration.

### 3. Install Dependencies

```yaml theme={null}
- name: Install pnpm
  uses: pnpm/action-setup@v2
  with:
    version: 10

- name: Install Xata CLI
  run: |
    curl -fsSL https://xata.io/install.sh | bash
    echo "/home/runner/.config/xata/bin" >> $GITHUB_PATH
```

These steps install the required dependencies:

* pnpm package manager
* Xata CLI

### 4. Check Status and Migration State

```yaml theme={null}
- name: Check Xata Status
  run: |
    xata status
    xata roll status
    xata branch view

- name: Check Xata main branch pgroll status
  id: xata-main-branch-roll-status
  run: |
    echo XATA_MAIN_BRANCH_ROLL_STATUS=`xata roll status | jq -r '.status'` >> $GITHUB_OUTPUT
```

These steps verify the current state of your Xata environment and check for any pending migrations.

### 5. Create or Update Comment

```yaml theme={null}
- name: Create or Update Comment
  id: create_comment
  uses: peter-evans/create-or-update-comment@v1
  with:
    token: ${{ secrets.GITHUB_TOKEN }}
    comment-id: ${{ steps.find-comment.outputs.comment-id }}
    issue-number: ${{ github.event.pull_request.number }}
    edit-mode: replace
    body: |
      ### Xata Dev Branch

      Creating Xata Dev Branch for commit: ${{ github.sha }}
      Branch name: ${{ github.head_ref }}_${{ github.event.pull_request.number }}

      *Please wait for the comment to be updated with branch details.*
```

### 6. Branch Management

```yaml theme={null}
- name: Delete Any Existing Xata Dev Branch
  run: xata branch delete ${{ github.head_ref }}_${{ github.event.pull_request.number }} --yes
  continue-on-error: true

- name: Create Xata Dev Branch
  run: xata branch create --name ${{ github.head_ref }}_${{ github.event.pull_request.number }}

- name: Checkout Xata Dev Branch
  run: |
    echo XATA_BRANCH_ID=`xata branch get ${{ github.head_ref }}_${{ github.event.pull_request.number }} id` >> $GITHUB_ENV
```

### 7. Migration Handling

```yaml theme={null}
- name: Complete Any Pending Migrations
  if: ${{ steps.xata-main-branch-roll-status.outputs.XATA_MAIN_BRANCH_ROLL_STATUS == 'In progress' }}
  run: |
    xata roll complete

- name: Apply Xata Roll Migrations
  run: xata roll migrate
```

### 8. Environment Setup

```yaml theme={null}
- name: Get Latest Schema
  id: xata-roll-get-latest-schema
  run: |
    echo LATEST_SCHEMA=`xata roll latest --with-schema=true` >> $GITHUB_OUTPUT

- name: Setup New Preview Environment
  run: |
    echo DATABASE_URL=`xata branch url` >> $GITHUB_ENV
    echo SEARCH_PATH=${{ steps.xata-roll-get-latest-schema.outputs.LATEST_SCHEMA }} >> $GITHUB_ENV
```

### 9. Final Comment Update

```yaml theme={null}
- name: Update Comment with Branch Details
  uses: peter-evans/create-or-update-comment@v1
  with:
    token: ${{ secrets.GITHUB_TOKEN }}
    comment-id: ${{ steps.find-comment.outputs.comment-id }}
    issue-number: ${{ github.event.pull_request.number }}
    edit-mode: replace
    body: |
      ### Xata Dev Branch

      Created Xata Dev Branch for commit: ${{ github.sha }}
      Branch name: ${{ github.head_ref }}_${{ github.event.pull_request.number }}

      Set the following schemas as the search_path:
          ${{ steps.xata-roll-get-latest-schema.outputs.LATEST_SCHEMA }}

      You can drop into the psql shell for this branch with:
          xata branch checkout ${{ github.head_ref }}_${{ github.event.pull_request.number }}
          psql `xata branch url`

      Branch URL: [View in Console](https://console.xata.io/organizations/123xyz/projects/prj_i3qsq56h3p3d3crnk7htnc75d0/branches/s99l2grl6d3nr1cqcbaaa5piic)
```

## Concurrency Control

The workflow uses GitHub Actions concurrency to prevent multiple runs from interfering with each other:

```yaml theme={null}
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

This ensures that:

* Only one workflow run per PR is active at a time
* New runs cancel any in-progress runs
* Prevents race conditions during branch creation

## When to Use

Use this workflow when you want to:

* Create isolated database environments for PRs
* Test database changes in isolation
* Provide preview environments for PR reviewers
* Automate database branch management

## Best Practices

1. **Set Appropriate Timeout**

   * The workflow has a 10-minute timeout
   * Adjust based on your typical migration duration

2. **Use Environment Variables**

   * Store sensitive information in GitHub secrets
   * Use environment variables for configuration

3. **Handle Migration States**

   * Check for in-progress migrations
   * Complete pending migrations
   * Apply new migrations

4. **PR Communication**
   * Keep PR comments updated
   * Provide clear connection instructions
   * Include relevant warnings

## Related Workflows

* [Check Workflow](./ga-check) - For validating main branch status
* [Clone Workflow](./ga-clone) - For cloning databases in CI/CD
