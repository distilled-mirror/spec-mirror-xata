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

> [!TIP]
>
> The CLI binary will be installed to `~/.config/xata/bin/`. Make sure this directory is in your PATH.

## Installation (Docker)

```bash theme={null}
# Pull docker image
docker pull ghcr.io/xataio/xata:latest-pg18
```

> [!TIP]
>
> The container image supports the last 3 PostgreSQL major releases: 16, 17 and 18. To select one, edit the image suffix `-pg{majorversion}`.

## Installation (Windows)

```bash theme={null}
# Install using npm
powershell -c "irm https://xata.io/install.ps1 | iex"
```

> [!TIP]
>
> `xata clone` currently doesn't work with the native Windows installation. We currently recommend using WSL with the Linux binaries if you need the clone functionality on Windows. Track progress [here](https://github.com/xataio/pgstream/issues/409).

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

* [`xata skill`](https://xata.io/docs/cli/skill) — Discover and install Xata agent skills
  * [`xata skill list`](https://xata.io/docs/cli/skill#list) — List available skills from xataio/skills
  * [`xata skill install`](https://xata.io/docs/cli/skill#install) — Install a skill; select interactively or list when no name is given
* [`xata init`](https://xata.io/docs/cli/init) — Link this folder to a project and branch
* [`xata auth`](https://xata.io/docs/cli/auth) — Authenticate with Xata
  * [`xata auth login`](https://xata.io/docs/cli/auth#login) — Log in to a Xata account
  * [`xata auth logout`](https://xata.io/docs/cli/auth#logout) — Log out of the current account
  * [`xata auth status`](https://xata.io/docs/cli/auth#status) — Display active account and authentication state
  * [`xata auth switch`](https://xata.io/docs/cli/auth#switch) — Switch to a different Xata account profile
  * [`xata auth list`](https://xata.io/docs/cli/auth#list) — List all available Xata account profiles
  * [`xata auth refresh`](https://xata.io/docs/cli/auth#refresh) — Refresh the access token of the current session
  * [`xata auth access-token`](https://xata.io/docs/cli/auth#access-token) — Print the current access token
  * [`xata auth refresh-token`](https://xata.io/docs/cli/auth#refresh-token) — Print the stored refresh token, without refreshing the session
* [`xata organization`](https://xata.io/docs/cli/organization) — Create, list, and manage organizations
  * [`xata organization list`](https://xata.io/docs/cli/organization#list) — List all organizations
  * [`xata organization describe`](https://xata.io/docs/cli/organization#describe) — Describe an organization
  * [`xata organization create`](https://xata.io/docs/cli/organization#create) — Create a new organization
  * [`xata organization delete`](https://xata.io/docs/cli/organization#delete) — Delete an organization
  * [`xata organization get`](https://xata.io/docs/cli/organization#get) — Get a field from an organization description
  * [`xata organization members`](https://xata.io/docs/cli/organization#members) — Manage organization members
    * [`xata organization members list`](https://xata.io/docs/cli/organization#members-list) — List all members of an organization
    * [`xata organization members invite`](https://xata.io/docs/cli/organization#members-invite) — Send an invitation to join an organization
    * [`xata organization members remove`](https://xata.io/docs/cli/organization#members-remove) — Remove a member from an organization
    * [`xata organization members set-role`](https://xata.io/docs/cli/organization#members-set-role) — Set the role of an organization member
  * [`xata organization invitations`](https://xata.io/docs/cli/organization#invitations) — Manage organization invitations
    * [`xata organization invitations list`](https://xata.io/docs/cli/organization#invitations-list) — List all invitations for an organization
    * [`xata organization invitations get`](https://xata.io/docs/cli/organization#invitations-get) — Get details of a specific invitation
    * [`xata organization invitations create`](https://xata.io/docs/cli/organization#invitations-create) — Create and send an invitation to join an organization
    * [`xata organization invitations delete`](https://xata.io/docs/cli/organization#invitations-delete) — Delete an invitation
    * [`xata organization invitations resend`](https://xata.io/docs/cli/organization#invitations-resend) — Resend an invitation
  * [`xata organization sso`](https://xata.io/docs/cli/organization#sso) — Configure single sign-on for an organization
    * [`xata organization sso show`](https://xata.io/docs/cli/organization#sso-show) — Show the domains and identity providers configured for an organization
    * [`xata organization sso domains`](https://xata.io/docs/cli/organization#sso-domains) — Claim and verify the email domains that sign in through your identity providers
      * [`xata organization sso domains add`](https://xata.io/docs/cli/organization#sso-domains-add) — Claim an email domain and print the DNS record that proves you own it
      * [`xata organization sso domains verify`](https://xata.io/docs/cli/organization#sso-domains-verify) — Check the DNS record for a claimed domain and mark it verified
      * [`xata organization sso domains remove`](https://xata.io/docs/cli/organization#sso-domains-remove) — Remove a claimed domain from an organization
    * [`xata organization sso providers`](https://xata.io/docs/cli/organization#sso-providers) — Connect the identity providers that sign in each verified domain
      * [`xata organization sso providers add`](https://xata.io/docs/cli/organization#sso-providers-add) — Connect an identity provider to a verified domain
      * [`xata organization sso providers remove`](https://xata.io/docs/cli/organization#sso-providers-remove) — Disconnect an identity provider from an organization
      * [`xata organization sso providers enforce`](https://xata.io/docs/cli/organization#sso-providers-enforce) — Require members on a domain to sign in through its identity provider
      * [`xata organization sso providers test`](https://xata.io/docs/cli/organization#sso-providers-test) — Print a link that tests sign-in through an identity provider
* [`xata project`](https://xata.io/docs/cli/project) — Create, list, and manage projects
  * [`xata project list`](https://xata.io/docs/cli/project#list) — List all projects
  * [`xata project describe`](https://xata.io/docs/cli/project#describe) — Describe a project
  * [`xata project create`](https://xata.io/docs/cli/project#create) — Create a new project
  * [`xata project delete`](https://xata.io/docs/cli/project#delete) — Delete a project
  * [`xata project init`](https://xata.io/docs/cli/project#init) — Link this folder to a project and branch
  * [`xata project get`](https://xata.io/docs/cli/project#get) — Get a field from a project description
  * [`xata project set`](https://xata.io/docs/cli/project#set) — Set a field value for a project
  * [`xata project ip-filter`](https://xata.io/docs/cli/project#ip-filter) — Manage IP filtering for a project
    * [`xata project ip-filter list`](https://xata.io/docs/cli/project#ip-filter-list) — Show IP filtering status and configured CIDR entries
    * [`xata project ip-filter enable`](https://xata.io/docs/cli/project#ip-filter-enable) — Enable IP filtering for a project
    * [`xata project ip-filter disable`](https://xata.io/docs/cli/project#ip-filter-disable) — Disable IP filtering for a project
    * [`xata project ip-filter add`](https://xata.io/docs/cli/project#ip-filter-add) — Add a CIDR entry to the IP filter allow list
    * [`xata project ip-filter remove`](https://xata.io/docs/cli/project#ip-filter-remove) — Remove a CIDR entry from the IP filter allow list
* [`xata branch`](https://xata.io/docs/cli/branch) — Create, list, and manage Xata branches
  * [`xata branch list`](https://xata.io/docs/cli/branch#list) — List all branches
  * [`xata branch describe`](https://xata.io/docs/cli/branch#describe) — Describe a branch
  * [`xata branch create`](https://xata.io/docs/cli/branch#create) — Create a new branch
  * [`xata branch delete`](https://xata.io/docs/cli/branch#delete) — Delete a branch
  * [`xata branch logs`](https://xata.io/docs/cli/branch#logs) — Retrieve the PostgreSQL logs of a branch
  * [`xata branch url`](https://xata.io/docs/cli/branch#url) — Print URL (connection string) for a branch
  * [`xata branch checkout`](https://xata.io/docs/cli/branch#checkout) — Check out a branch in this folder
  * [`xata branch tree`](https://xata.io/docs/cli/branch#tree) — List all branches as a tree
  * [`xata branch get`](https://xata.io/docs/cli/branch#get) — Get a field from a branch description
  * [`xata branch metrics`](https://xata.io/docs/cli/branch#metrics) — Show CPU, memory and disk usage for a branch
  * [`xata branch set`](https://xata.io/docs/cli/branch#set) — Set a field value for a branch
  * [`xata branch rotate-password`](https://xata.io/docs/cli/branch#rotate-password) — Rotate the database password for a branch
  * [`xata branch wait-ready`](https://xata.io/docs/cli/branch#wait-ready) — Wait for a branch to be ready
  * [`xata branch query-insights`](https://xata.io/docs/cli/branch#query-insights) — Inspect query statistics and active queries for a branch
    * [`xata branch query-insights list`](https://xata.io/docs/cli/branch#query-insights-list) — List historical query statistics for a branch, by total execution time
    * [`xata branch query-insights show`](https://xata.io/docs/cli/branch#query-insights-show) — Show full query statistics for a query ID
    * [`xata branch query-insights active`](https://xata.io/docs/cli/branch#query-insights-active) — List currently running queries for a branch
    * [`xata branch query-insights enable`](https://xata.io/docs/cli/branch#query-insights-enable) — Enable pg\_stat\_statements for query insights on a branch
    * [`xata branch query-insights reset`](https://xata.io/docs/cli/branch#query-insights-reset) — Reset accumulated query statistics for a branch
* [`xata keys`](https://xata.io/docs/cli/keys) — Create, list, and delete API keys
  * [`xata keys user`](https://xata.io/docs/cli/keys#user) — Manage API keys for the current user
    * [`xata keys user list`](https://xata.io/docs/cli/keys#user-list) — List all API keys of the current user
    * [`xata keys user create`](https://xata.io/docs/cli/keys#user-create) — Create a new API key
    * [`xata keys user delete`](https://xata.io/docs/cli/keys#user-delete) — Delete one or more API keys
  * [`xata keys organization`](https://xata.io/docs/cli/keys#organization) — Manage API keys for an organization
    * [`xata keys organization list`](https://xata.io/docs/cli/keys#organization-list) — List all API keys for an organization
    * [`xata keys organization create`](https://xata.io/docs/cli/keys#organization-create) — Create a new API key
    * [`xata keys organization delete`](https://xata.io/docs/cli/keys#organization-delete) — Delete one or more API keys
* [`xata roll`](https://xata.io/docs/cli/roll) — Run pgroll to manage PostgreSQL schema migrations
  * [`xata roll baseline`](https://xata.io/docs/cli/roll#baseline) — Create a baseline migration for an existing database schema
  * [`xata roll complete`](https://xata.io/docs/cli/roll#complete) — Complete an ongoing migration with the operations present in the given file
  * [`xata roll init`](https://xata.io/docs/cli/roll#init) — Initialize pgroll in the target database
  * [`xata roll latest`](https://xata.io/docs/cli/roll#latest) — Print the name of the latest schema version or migration
    * [`xata roll latest migration`](https://xata.io/docs/cli/roll#latest-migration) — Print the latest migration name
    * [`xata roll latest schema`](https://xata.io/docs/cli/roll#latest-schema) — Print the latest version schema name
  * [`xata roll migrate`](https://xata.io/docs/cli/roll#migrate) — Apply outstanding migrations from a directory to a database
  * [`xata roll update`](https://xata.io/docs/cli/roll#update) — Update outdated migrations in a directory
  * [`xata roll pull`](https://xata.io/docs/cli/roll#pull) — Pull migration history from the target database and write it to disk
  * [`xata roll rollback`](https://xata.io/docs/cli/roll#rollback) — Roll back an ongoing migration
  * [`xata roll start`](https://xata.io/docs/cli/roll#start) — Start a migration for the operations present in the given file
  * [`xata roll status`](https://xata.io/docs/cli/roll#status) — Show pgroll status
  * [`xata roll convert`](https://xata.io/docs/cli/roll#convert) — Convert SQL statements to a pgroll migration
* [`xata clone`](https://xata.io/docs/cli/clone) — Clone another PostgreSQL database with anonymization
  * [`xata clone start`](https://xata.io/docs/cli/clone#start) — Snapshot a PostgreSQL database into a Xata branch
  * [`xata clone config`](https://xata.io/docs/cli/clone#config) — Write the anonymization rules that clone start and stream apply
  * [`xata clone stream`](https://xata.io/docs/cli/clone#stream) — Stream a PostgreSQL database into a Xata branch continuously
* [`xata status`](https://xata.io/docs/cli/status) — Show the organization, project, branch, and database this folder uses
* [`xata version`](https://xata.io/docs/cli/version) — Get the version of the Xata CLI, pgroll and pgstream
* [`xata licenses`](https://xata.io/docs/cli/licenses) — Show third-party software notices and licenses bundled with the Xata CLI
* [`xata checkout`](https://xata.io/docs/cli/checkout) — Check out a branch in this folder
* [`xata scratch`](https://xata.io/docs/cli/scratch) — Run SQL or a Postgres client command against a temporary scratch branch
* [`xata console`](https://xata.io/docs/cli/console) — Open an interactive branch console
* [`xata upgrade`](https://xata.io/docs/cli/upgrade) — Upgrade the Xata CLI
* [`xata completions`](https://xata.io/docs/cli/completions) — Install or uninstall shell completions for the Xata CLI
  * [`xata completions install`](https://xata.io/docs/cli/completions#install) — Installs bash autocomplete support for xata
  * [`xata completions uninstall`](https://xata.io/docs/cli/completions#uninstall) — Uninstalls bash autocomplete support for xata

## Global Flags

**-h, --help** `boolean`

Print help information and exit

**-v, --version** `boolean`

Print version information and exit

**--json** `boolean`

Output in JSON format (where applicable)

## Required scopes

Most commands work with a key scoped to `branch:read`. These need more:

| Command | Scope | Why |
| - | - | - |
| [`branch logs`](https://xata.io/docs/cli/branch#logs) | `logs:read` | Reads server logs through the Xata API |
| [`branch url`](https://xata.io/docs/cli/branch#url) | `credentials:read` | Reads connection details from the credentials endpoint |
| [`branch rotate-password`](https://xata.io/docs/cli/branch#rotate-password) | `credentials:read` | Reads the current username before rotating |
| [`branch query-insights`](https://xata.io/docs/cli/branch#query-insights) | `credentials:read` | Opens a direct PostgreSQL connection to the branch |
| [`roll url`](https://xata.io/docs/cli/roll) | `credentials:read` | Reads connection details from the credentials endpoint |
| [`project init`](https://xata.io/docs/cli/project) | `credentials:read` | Reads connection details from the credentials endpoint |
| [`clone start`](https://xata.io/docs/cli/clone), [`stream`](https://xata.io/docs/cli/stream) | `credentials:read` | Connects to the source and target branches |

A key that only has `branch:read` returns a 401 on the commands above. Scopes are set when you create the key, so a key created before a scope existed does not have it. See [API keys](https://xata.io/docs/platform/api-key.md) for how to create and scope one.

Commands that manage members, invitations, roles, organization API keys or single sign-on also need an Admin of the organization. See [Organization roles](https://xata.io/docs/platform/organization-roles.md).

## Environment Variables

The Xata CLI can be configured using environment variables. These are useful for CI/CD pipelines, automation workflows, and advanced configuration.

### Authentication & Configuration

These variables apply globally to all CLI commands.

**XATA\_API\_KEY** `string`

API key used to authenticate with Xata. In CI/CD environments, set this as a secret. Applies to all commands that require authentication.

**XATA\_API\_ENVIRONMENT** `string`

Select the Xata API environment. Typically not needed unless instructed by Xata support. Applies to all commands.

**XATA\_CONFIG\_DIR** `string`

Override the directory where the CLI stores its configuration files. Applies to all commands.

### Project & Branch Configuration

These environment variables override the project and branch configuration normally stored in local config files. They apply to all commands that require project or branch context and are especially useful in CI/CD workflows where the CLI is not initialized interactively.

> [!NOTE]
>
> Earlier CLI versions used names without an underscore between words (for example `XATA_ORGANIZATIONID`, `XATA_PROJECTID`, `XATA_BRANCHID`, `XATA_BRANCHNAME`, `XATA_DATABASENAME`). These legacy names are still supported, but the snake\_case names below are preferred and take precedence when both are set.

**XATA\_ORGANIZATION\_ID** `string`

Your Xata organization ID.

**XATA\_PROJECT\_ID** `string`

Your Xata project ID.

**XATA\_BRANCH\_ID** `string`

The ID of the target branch.

**XATA\_BRANCH\_NAME** `string`

The name of the target branch.

**XATA\_DATABASE\_NAME** `string`

The database name (default: `xata`).

### Binary Version Overrides

The CLI ships with pinned versions of the `pgroll` and `pgstream` binaries. These environment variables allow you to override the pinned version, for example to test a newer release or to pin a specific version in your CI/CD pipeline. The CLI will automatically download the specified version if it is not already present locally.

**XATA\_PGROLL\_BINARY\_VERSION** `string`

Override the pinned `pgroll` binary version used by the CLI. If not set, the CLI uses its built-in default. Applies to all `xata roll` subcommands, `xata version`, and `xata upgrade`.

**XATA\_PGSTREAM\_BINARY\_VERSION** `string`

Override the pinned `pgstream` binary version used by the CLI. If not set, the CLI uses its built-in default. Applies to all `xata stream` subcommands, all `xata clone` subcommands, `xata version`, and `xata upgrade`.

### Clone & Stream

**XATA\_CLI\_SOURCE\_POSTGRES\_URL** `string`

Source PostgreSQL URL. Can be used instead of the `--source-url` flag. Applies to `xata clone start`, `xata clone stream`, and `xata stream destroy`.

### Networking

**XATA\_PRIVATE\_BRANCH\_TIMEOUT** `string`

Timeout in milliseconds for the private branch reachability check (default: `1000`). Set to `0` to disable the check entirely. Applies to all `xata roll` and `xata clone` subcommands.

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


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
