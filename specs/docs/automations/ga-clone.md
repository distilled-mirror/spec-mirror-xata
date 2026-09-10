> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Scheduled Clone

> GitHub Actions workflow for cloning databases on a schedule or on-demand

This workflow helps you clone your database on a schedule or manually trigger it. It's useful for creating regular backups or setting up test environments.

## Workflow configuration

```yaml theme={null}
name: Xata clone (daily, on-demand)

on:
  schedule:
    - cron: "0 0 * * *" # Run daily at midnight
  workflow_dispatch: # Allow manual triggers

env:
  XATA_API_KEY: ${{ secrets.XATA_API_KEY }}
  XATA_ORGANIZATION_ID: ${{ secrets.XATA_ORGANIZATION_ID }}
  XATA_PROJECT_ID: ${{ secrets.XATA_PROJECT_ID }}
  XATA_BRANCH_ID: ${{ secrets.XATA_BRANCH_ID }}
  XATA_BRANCH_NAME: ${{ secrets.XATA_BRANCH_NAME }}
  XATA_DATABASE_NAME: ${{ secrets.XATA_DATABASE_NAME }}
  XATA_CLI_SOURCE_POSTGRES_URL: ${{ secrets.XATA_CLI_SOURCE_POSTGRES_URL }}

jobs:
  clone:
    name: Xata Clone
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Install Xata CLI
        run: |
          curl -fsSL https://xata.io/install.sh | bash
          echo "/home/runner/.config/xata/bin" >> $GITHUB_PATH

      - name: Check Xata status
        run: |
          xata status
          xata branch describe

      - name: Xata clone
        run: |
          xata clone start --source-url ${{ env.XATA_CLI_SOURCE_POSTGRES_URL }}
```

## Authentication and environment variables

The workflow requires several environment variables to be set:

* `XATA_API_KEY`: Your Xata API key (set as a GitHub secret)
* `XATA_ORGANIZATION_ID`: Your Xata organization ID
* `XATA_PROJECT_ID`: Your Xata project ID
* `XATA_BRANCH_ID`: The ID of the main branch
* `XATA_BRANCH_NAME`: The name of the main branch
* `XATA_DATABASE_NAME`: Your database name
* `XATA_CLI_SOURCE_POSTGRES_URL`: The source PostgreSQL database URL (set as a GitHub secret)

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

### 1. Checkout code

```yaml theme={null}
- uses: actions/checkout@v3
  with:
    fetch-depth: 0
```

This step checks out your repository code with full history and proper credentials configuration.

### 2. Install Xata CLI

```yaml theme={null}
- name: Install Xata CLI
  run: |
    curl -fsSL https://xata.io/install.sh | bash
```

This step installs the Xata CLI using the official installer script and adds it to the GitHub Actions PATH.

### 3. Check Xata status

```yaml theme={null}
- name: Check Xata status
  run: |
    xata status
    xata branch describe
```

This step verifies that your Xata CLI is properly configured and can connect to your database.

For more details, see:

* [status command documentation](../cli/status)
* [branch describe command documentation](../cli/branch#describe)

### 4. Checkout clone branch

```yaml theme={null}
- name: Checkout Xata dev branch
  run: |
    echo XATA_BRANCH_ID=`xata branch get clone_demo id` >> $GITHUB_ENV
```

This step:

* Checks out the clone\_demo branch
* Extracts the branch ID using `jq`
* Stores it in an environment variable

### 5. Start clone process

```yaml theme={null}
- name: Xata clone
  run: |
    xata clone start --source-url ${{ env.XATA_CLI_SOURCE_POSTGRES_URL }}
```

This step initiates the cloning process from your source PostgreSQL database.

For more details, see the [clone start command documentation](../cli/clone#start).

## Triggering the workflow

The workflow can be triggered in two ways:

1. **Scheduled run**

   ```yaml theme={null}
   schedule:
     - cron: "0 8 * * *"
   ```

   Runs daily at 8:00 AM UTC

2. **Manual trigger**
   ```yaml theme={null}
   workflow_dispatch:
   ```
   Can be triggered manually from the GitHub Actions UI

## Concurrency control

The workflow uses GitHub Actions concurrency to prevent multiple runs from interfering with each other:

```yaml theme={null}
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

This ensures that:

* Only one workflow run is active at a time
* New runs cancel any in-progress runs
* Prevents race conditions during cloning

## Best practices

1. **Set appropriate timeout**

   * The workflow has a 10-minute timeout
   * Adjust based on your database size and cloning duration

2. **Use environment variables**

   * Store sensitive information in GitHub secrets
   * Use environment variables for configuration

3. **Schedule regular clones**
   * Set up a schedule that matches your backup needs
   * Consider your database size and update frequency

## Related workflows

* [Check workflow](./ga-check) - For validating main branch status
* [PR workflow](./ga-pr) - For managing pull request environments
