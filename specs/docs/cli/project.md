> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# project

> Create, list, and manage projects

A project holds branches and the defaults they inherit, such as whether they scale to zero and how long they stay idle first. IP filtering is set per project.

Every command below also takes `-h, --help`.

* [`xata project list`](#list) — List all projects
* [`xata project describe`](#describe) — Describe a project
* [`xata project create`](#create) — Create a new project
* [`xata project delete`](#delete) — Delete a project
* [`xata project init`](#init) — Link this folder to a project and branch
* [`xata project get`](#get) — Get a field from a project description
* [`xata project set`](#set) — Set a field value for a project
* [`xata project ip-filter`](#ip-filter) — Manage IP filtering for a project
  * [`xata project ip-filter list`](#ip-filter-list) — Show IP filtering status and configured CIDR entries
  * [`xata project ip-filter enable`](#ip-filter-enable) — Enable IP filtering for a project
  * [`xata project ip-filter disable`](#ip-filter-disable) — Disable IP filtering for a project
  * [`xata project ip-filter add`](#ip-filter-add) — Add a CIDR entry to the IP filter allow list
  * [`xata project ip-filter remove`](#ip-filter-remove) — Remove a CIDR entry from the IP filter allow list

## list

List all projects

```bash theme={null}
xata project list [--organization value] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**Aliases:** `xata project ls`

## describe

Describe a project

```bash theme={null}
xata project describe [--organization value] [--project value] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**Aliases:** `xata project view`, `xata project show`

## create

Create a new project

```bash theme={null}
xata project create [--organization value] (--name value) [--branch-name value] [--description value] [--instance-type value] [--replicas value] [--region value] [--storage value] [--postgres-version value] [--scale-to-zero-base true|false] [--scale-to-zero-child true|false] [--inactivity-period-base value] [--inactivity-period-child value] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--name** `string` *required*

Project Name

**--branch-name** `string`

Branch Name

**--description** `string`

Short description of what the first branch is for

**--instance-type** `string`

Instance type for the first branch

**--replicas** `string`

Number of read replicas for the first branch

**--region** `string`

Region to create the project in

**--storage** `string`

Storage in GB for the first branch

**--postgres-version** `string`

Please select the PostgreSQL version for this branch

**--scale-to-zero-base** `true | false`

Default scale to zero status for base branches

**--scale-to-zero-child** `true | false`

Default scale to zero status for child branches

**--inactivity-period-base** `string`

Default inactivity period for base branches in whole minutes (1–1440)

**--inactivity-period-child** `string`

Default inactivity period for child branches in whole minutes (1–1440)

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

## delete

Delete a project

```bash theme={null}
xata project delete [--organization value] [--project value] [--yes] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--yes** `boolean` (default: false)

Do not ask for confirmation, assume yes.

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

## init

Link this folder to a project and branch

Writes the organization, project, branch and database to `.xata/` in this folder, so the commands run here no longer need them passed in.

```bash theme={null}
xata project init [--organization value] [--project value] [--branch value] [--database value] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--database** `string`

Database name on the branch

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**Examples:**

```bash theme={null}
# Link without prompts
xata project init --organization <org-id> --project <project-id> --branch <branch-id>
```

**Aliases:** `xata project connect`, `xata project switch`, `xata project link`

## get

Get a field from a project description

```bash theme={null}
xata project get [--organization value] [--project value] [--profile value] [--debug] [--json] <[project] field>...
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**\[project] field** `string`

Project name and/or field to get

## set

Set a field value for a project

The fields are name, scale-to-zero-base, scale-to-zero-child, inactivity-period-base and inactivity-period-child. The scale to zero and inactivity fields are the defaults new branches inherit, base for branches without a parent and child for the rest. Run it without a field to list them.

```bash theme={null}
xata project set [--organization value] [--project value] [--profile value] [--debug] [--json] [<field>] [<value>]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**field** `string`

The field to set

**value** `string`

The value to set

**Examples:**

```bash theme={null}
# Let new child branches scale to zero
xata project set scale-to-zero-child true
```

## ip-filter

Manage IP filtering for a project

### ip-filter list

Show IP filtering status and configured CIDR entries

```bash theme={null}
xata project ip-filter list [--organization value] [--project value] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

### ip-filter enable

Enable IP filtering for a project

```bash theme={null}
xata project ip-filter enable [--organization value] [--project value] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

### ip-filter disable

Disable IP filtering for a project

```bash theme={null}
xata project ip-filter disable [--organization value] [--project value] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

### ip-filter add

Add a CIDR entry to the IP filter allow list

```bash theme={null}
xata project ip-filter add [--organization value] [--project value] [--label value] [--profile value] [--debug] [--json] [<cidr>]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--label** `string`

Label for the CIDR entry

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**cidr** `string`

IP address or CIDR block to add (e.g. 192.168.0.0/24 or 10.0.0.1)

**Examples:**

```bash theme={null}
# Allow a single address
xata project ip-filter add 203.0.113.4
# Allow a range and label it
xata project ip-filter add 203.0.113.0/24 --label office
```

### ip-filter remove

Remove a CIDR entry from the IP filter allow list

```bash theme={null}
xata project ip-filter remove [--organization value] [--project value] [--force] [--profile value] [--debug] [--json] [<cidr>]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--force** `boolean` (default: false)

Skip confirmation prompts

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**cidr** `string`

CIDR block to remove


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
