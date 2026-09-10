> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Command-line Interface

> Complete reference for the Xata CLI

The Xata CLI is a powerful tool for managing your Xata databases, projects, and organizations from the command line. It provides commands for authentication, project management, database operations, and more.

## Installation

```bash theme={null}
# Install using the official install script
curl -fsSL https://xata.io/install.sh | bash
```

<Tip>
  The CLI binary will be installed to `~/.config/xata/bin/`. Make sure this directory is in your PATH.
</Tip>

## Installation (Docker)

```bash theme={null}
# Pull docker image
docker pull ghcr.io/xataio/xata:latest-pg18
```

<Tip>
  The container image supports the last 3 PostgreSQL major releases: 16, 17 and 18. To select one, edit the image suffix `-pg{majorversion}`.
</Tip>

## Installation (Windows)

```bash theme={null}
# Install using npm
powershell -c "irm https://xata.io/install.ps1 | iex"
```

<Tip>
  `xata clone` currently doesn't work with the native Windows installation. We currently recommend using WSL with the Linux binaries if you need the clone functionality on Windows. Track progress [here](https://github.com/xataio/pgstream/issues/409).
</Tip>

## Basic Usage

```bash theme={null}
xata [command] [subcommand] [options]
```

### Container image

```bash theme={null}
docker run -e XATA_API_KEY=yourkey ghcr.io/xataio/xata:latest-pg18 [command] [subcommand] [options]
```

## Available Commands

Every command page opens with an index of the commands on it.

* [`xata init`](/docs/cli/init) — Link this folder to a project and branch
* [`xata auth`](/docs/cli/auth) — Authenticate with Xata
  * [`xata auth login`](/docs/cli/auth#login) — Log in to a Xata account
  * [`xata auth logout`](/docs/cli/auth#logout) — Log out of the current account
  * [`xata auth status`](/docs/cli/auth#status) — Display active account and authentication state
  * [`xata auth switch`](/docs/cli/auth#switch) — Switch to a different Xata account profile
  * [`xata auth list`](/docs/cli/auth#list) — List all available Xata account profiles
  * [`xata auth refresh`](/docs/cli/auth#refresh) — Refresh the access token of the current session
  * [`xata auth access-token`](/docs/cli/auth#access-token) — Print the current access token
  * [`xata auth refresh-token`](/docs/cli/auth#refresh-token) — Print the stored refresh token, without refreshing the session
* [`xata organization`](/docs/cli/organization) — Create, list, and manage organizations
  * [`xata organization list`](/docs/cli/organization#list) — List all organizations
  * [`xata organization describe`](/docs/cli/organization#describe) — Describe an organization
  * [`xata organization create`](/docs/cli/organization#create) — Create a new organization
  * [`xata organization delete`](/docs/cli/organization#delete) — Delete an organization
  * [`xata organization get`](/docs/cli/organization#get) — Get a field from an organization description
  * [`xata organization members`](/docs/cli/organization#members) — Manage organization members
    * [`xata organization members list`](/docs/cli/organization#members-list) — List all members of an organization
    * [`xata organization members invite`](/docs/cli/organization#members-invite) — Send an invitation to join an organization
    * [`xata organization members remove`](/docs/cli/organization#members-remove) — Remove a member from an organization
  * [`xata organization invitations`](/docs/cli/organization#invitations) — Manage organization invitations
    * [`xata organization invitations list`](/docs/cli/organization#invitations-list) — List all invitations for an organization
    * [`xata organization invitations get`](/docs/cli/organization#invitations-get) — Get details of a specific invitation
    * [`xata organization invitations create`](/docs/cli/organization#invitations-create) — Create and send an invitation to join an organization
    * [`xata organization invitations delete`](/docs/cli/organization#invitations-delete) — Delete an invitation
    * [`xata organization invitations resend`](/docs/cli/organization#invitations-resend) — Resend an invitation
* [`xata project`](/docs/cli/project) — Create, list, and manage projects
  * [`xata project list`](/docs/cli/project#list) — List all projects
  * [`xata project describe`](/docs/cli/project#describe) — Describe a project
  * [`xata project create`](/docs/cli/project#create) — Create a new project
  * [`xata project delete`](/docs/cli/project#delete) — Delete a project
  * [`xata project init`](/docs/cli/project#init) — Link this folder to a project and branch
  * [`xata project get`](/docs/cli/project#get) — Get a field from a project description
  * [`xata project set`](/docs/cli/project#set) — Set a field value for a project
  * [`xata project ip-filter`](/docs/cli/project#ip-filter) — Manage IP filtering for a project
    * [`xata project ip-filter list`](/docs/cli/project#ip-filter-list) — Show IP filtering status and configured CIDR entries
    * [`xata project ip-filter enable`](/docs/cli/project#ip-filter-enable) — Enable IP filtering for a project
    * [`xata project ip-filter disable`](/docs/cli/project#ip-filter-disable) — Disable IP filtering for a project
    * [`xata project ip-filter add`](/docs/cli/project#ip-filter-add) — Add a CIDR entry to the IP filter allow list
    * [`xata project ip-filter remove`](/docs/cli/project#ip-filter-remove) — Remove a CIDR entry from the IP filter allow list
* [`xata branch`](/docs/cli/branch) — Create, list, and manage Xata branches
  * [`xata branch list`](/docs/cli/branch#list) — List all branches
  * [`xata branch describe`](/docs/cli/branch#describe) — Describe a branch
  * [`xata branch create`](/docs/cli/branch#create) — Create a new branch
  * [`xata branch delete`](/docs/cli/branch#delete) — Delete a branch
  * [`xata branch logs`](/docs/cli/branch#logs) — Retrieve the PostgreSQL logs of a branch
  * [`xata branch url`](/docs/cli/branch#url) — Print URL (connection string) for a branch
  * [`xata branch checkout`](/docs/cli/branch#checkout) — Check out a branch in this folder
  * [`xata branch tree`](/docs/cli/branch#tree) — List all branches as a tree
  * [`xata branch get`](/docs/cli/branch#get) — Get a field from a branch description
  * [`xata branch metrics`](/docs/cli/branch#metrics) — Show CPU, memory and disk usage for a branch
  * [`xata branch set`](/docs/cli/branch#set) — Set a field value for a branch
  * [`xata branch rotate-password`](/docs/cli/branch#rotate-password) — Rotate the database password for a branch
  * [`xata branch wait-ready`](/docs/cli/branch#wait-ready) — Wait for a branch to be ready
  * [`xata branch query-insights`](/docs/cli/branch#query-insights) — Inspect query statistics and active queries for a branch
    * [`xata branch query-insights list`](/docs/cli/branch#query-insights-list) — List historical query statistics for a branch, by total execution time
    * [`xata branch query-insights show`](/docs/cli/branch#query-insights-show) — Show full query statistics for a query ID
    * [`xata branch query-insights active`](/docs/cli/branch#query-insights-active) — List currently running queries for a branch
    * [`xata branch query-insights enable`](/docs/cli/branch#query-insights-enable) — Enable pg\_stat\_statements for query insights on a branch
    * [`xata branch query-insights reset`](/docs/cli/branch#query-insights-reset) — Reset accumulated query statistics for a branch
* [`xata keys`](/docs/cli/keys) — Create, list, and delete API keys
  * [`xata keys user`](/docs/cli/keys#user) — Manage API keys for the current user
    * [`xata keys user list`](/docs/cli/keys#user-list) — List all API keys of the current user
    * [`xata keys user create`](/docs/cli/keys#user-create) — Create a new API key
    * [`xata keys user delete`](/docs/cli/keys#user-delete) — Delete one or more API keys
  * [`xata keys organization`](/docs/cli/keys#organization) — Manage API keys for an organization
    * [`xata keys organization list`](/docs/cli/keys#organization-list) — List all API keys for an organization
    * [`xata keys organization create`](/docs/cli/keys#organization-create) — Create a new API key
    * [`xata keys organization delete`](/docs/cli/keys#organization-delete) — Delete one or more API keys
* [`xata roll`](/docs/cli/roll) — Run pgroll to manage PostgreSQL schema migrations
  * [`xata roll baseline`](/docs/cli/roll#baseline) — Create a baseline migration for an existing database schema
  * [`xata roll complete`](/docs/cli/roll#complete) — Complete an ongoing migration with the operations present in the given file
  * [`xata roll init`](/docs/cli/roll#init) — Initialize pgroll in the target database
  * [`xata roll latest`](/docs/cli/roll#latest) — Print the name of the latest schema version or migration
    * [`xata roll latest migration`](/docs/cli/roll#latest-migration) — Print the latest migration name
    * [`xata roll latest schema`](/docs/cli/roll#latest-schema) — Print the latest version schema name
  * [`xata roll migrate`](/docs/cli/roll#migrate) — Apply outstanding migrations from a directory to a database
  * [`xata roll update`](/docs/cli/roll#update) — Update outdated migrations in a directory
  * [`xata roll pull`](/docs/cli/roll#pull) — Pull migration history from the target database and write it to disk
  * [`xata roll rollback`](/docs/cli/roll#rollback) — Roll back an ongoing migration
  * [`xata roll start`](/docs/cli/roll#start) — Start a migration for the operations present in the given file
  * [`xata roll status`](/docs/cli/roll#status) — Show pgroll status
  * [`xata roll convert`](/docs/cli/roll#convert) — Convert SQL statements to a pgroll migration
* [`xata clone`](/docs/cli/clone) — Clone another PostgreSQL database with anonymization
  * [`xata clone start`](/docs/cli/clone#start) — Snapshot a PostgreSQL database into a Xata branch
  * [`xata clone config`](/docs/cli/clone#config) — Write the anonymization rules that clone start and stream apply
  * [`xata clone stream`](/docs/cli/clone#stream) — Stream a PostgreSQL database into a Xata branch continuously
* [`xata status`](/docs/cli/status) — Show the organization, project, and branch this folder uses
* [`xata version`](/docs/cli/version) — Get the version of the Xata CLI, pgroll and pgstream
* [`xata checkout`](/docs/cli/checkout) — Check out a branch in this folder
* [`xata scratch`](/docs/cli/scratch) — Run SQL or a Postgres client command against a temporary scratch branch
* [`xata upgrade`](/docs/cli/upgrade) — Upgrade the Xata CLI
* [`xata completions`](/docs/cli/completions) — Install or uninstall shell completions for the Xata CLI
  * [`xata completions install`](/docs/cli/completions#install) — Installs bash autocomplete support for xata
  * [`xata completions uninstall`](/docs/cli/completions#uninstall) — Uninstalls bash autocomplete support for xata

## Global Flags

<ParamField path="-h, --help" type="boolean">
  Print help information and exit
</ParamField>

<ParamField path="-v, --version" type="boolean">
  Print version information and exit
</ParamField>

<ParamField path="--json" type="boolean">
  Output in JSON format (where applicable)
</ParamField>

## Required scopes

Most commands work with a key scoped to `branch:read`. These need more:

| Command                                                 | Scope              | Why                                                    |
| ------------------------------------------------------- | ------------------ | ------------------------------------------------------ |
| [`branch logs`](/docs/cli/branch#logs)                       | `logs:read`        | Reads server logs through the Xata API                 |
| [`branch url`](/docs/cli/branch#url)                         | `credentials:read` | Reads connection details from the credentials endpoint |
| [`branch rotate-password`](/docs/cli/branch#rotate-password) | `credentials:read` | Reads the current username before rotating             |
| [`branch query-insights`](/docs/cli/branch#query-insights)   | `credentials:read` | Opens a direct PostgreSQL connection to the branch     |
| [`roll url`](/docs/cli/roll)                                 | `credentials:read` | Reads connection details from the credentials endpoint |
| [`project init`](/docs/cli/project)                          | `credentials:read` | Reads connection details from the credentials endpoint |
| [`clone start`](/docs/cli/clone), [`stream`](/docs/cli/stream)    | `credentials:read` | Connects to the source and target branches             |

A key that only has `branch:read` returns a 401 on the commands above. Scopes are set when you create the key, so a key created before a scope existed does not have it. See [API keys](/docs/platform/api-key) for how to create and scope one.

## Environment Variables

The Xata CLI can be configured using environment variables. These are useful for CI/CD pipelines, automation workflows, and advanced configuration.

### Authentication & Configuration

These variables apply globally to all CLI commands.

<ParamField path="XATA_API_KEY" type="string">
  API key used to authenticate with Xata. In CI/CD environments, set this as a secret. Applies to all commands that require authentication.
</ParamField>

<ParamField path="XATA_API_ENVIRONMENT" type="string">
  Select the Xata API environment. Typically not needed unless instructed by Xata support. Applies to all commands.
</ParamField>

<ParamField path="XATA_CONFIG_DIR" type="string">
  Override the directory where the CLI stores its configuration files. Applies to all commands.
</ParamField>

### Project & Branch Configuration

These environment variables override the project and branch configuration normally stored in local config files. They apply to all commands that require project or branch context and are especially useful in CI/CD workflows where the CLI is not initialized interactively.

<Note>
  Earlier CLI versions used names without an underscore between words (for example `XATA_ORGANIZATIONID`, `XATA_PROJECTID`, `XATA_BRANCHID`, `XATA_BRANCHNAME`, `XATA_DATABASENAME`). These legacy names are still supported, but the snake\_case names below are preferred and take precedence when both are set.
</Note>

<ParamField path="XATA_ORGANIZATION_ID" type="string">
  Your Xata organization ID.
</ParamField>

<ParamField path="XATA_PROJECT_ID" type="string">
  Your Xata project ID.
</ParamField>

<ParamField path="XATA_BRANCH_ID" type="string">
  The ID of the target branch.
</ParamField>

<ParamField path="XATA_BRANCH_NAME" type="string">
  The name of the target branch.
</ParamField>

<ParamField path="XATA_DATABASE_NAME" type="string">
  The database name (default: `xata`).
</ParamField>

### Binary Version Overrides

The CLI ships with pinned versions of the `pgroll` and `pgstream` binaries. These environment variables allow you to override the pinned version, for example to test a newer release or to pin a specific version in your CI/CD pipeline. The CLI will automatically download the specified version if it is not already present locally.

<ParamField path="XATA_PGROLL_BINARY_VERSION" type="string">
  Override the pinned `pgroll` binary version used by the CLI. If not set, the CLI uses its built-in default. Applies to all `xata roll` subcommands, `xata version`, and `xata upgrade`.
</ParamField>

<ParamField path="XATA_PGSTREAM_BINARY_VERSION" type="string">
  Override the pinned `pgstream` binary version used by the CLI. If not set, the CLI uses its built-in default. Applies to all `xata stream` subcommands, all `xata clone` subcommands, `xata version`, and `xata upgrade`.
</ParamField>

### Clone & Stream

<ParamField path="XATA_CLI_SOURCE_POSTGRES_URL" type="string">
  Source PostgreSQL URL. Can be used instead of the `--source-url` flag. Applies to `xata clone start`, `xata clone stream`, and `xata stream destroy`.
</ParamField>

### Networking

<ParamField path="XATA_PRIVATE_BRANCH_TIMEOUT" type="string">
  Timeout in milliseconds for the private branch reachability check (default: `1000`). Set to `0` to disable the check entirely. Applies to all `xata roll` and `xata clone` subcommands.
</ParamField>

### Examples

```bash theme={null}
# Override the pgroll binary version for a single command
XATA_PGROLL_BINARY_VERSION=0.16.2 xata roll status

# Set environment variables for a CI/CD pipeline
export XATA_API_KEY="your-api-key"
export XATA_ORGANIZATION_ID="your-org-id"
export XATA_PROJECT_ID="your-project-id"
export XATA_BRANCH_ID="your-branch-id"
xata roll migrate --complete
```
