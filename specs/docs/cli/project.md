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
xata project list [--organization value] [--json] [--profile value] [--debug]
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--json" type="boolean" default="false">
  Output in JSON format
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

**Aliases:** `xata project ls`

## describe

Describe a project

```bash theme={null}
xata project describe [--organization value] [--project value] [--json] [--profile value] [--debug]
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--project" type="string">
  Project ID
</ParamField>

<ParamField path="--json" type="boolean" default="false">
  Output in JSON format
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

**Aliases:** `xata project view`, `xata project show`

## create

Create a new project

```bash theme={null}
xata project create [--organization value] (--name value) [--branch-name value] [--instance-type value] [--replicas value] [--region value] [--postgres-version value] [--scale-to-zero-base true|false] [--scale-to-zero-child true|false] [--inactivity-period-base 15|30|60|120|180] [--inactivity-period-child 15|30|60|120|180] [--json] [--profile value] [--debug]
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--name" type="string" required>
  Project Name
</ParamField>

<ParamField path="--branch-name" type="string">
  Branch Name
</ParamField>

<ParamField path="--instance-type" type="string">
  Instance type for the first branch
</ParamField>

<ParamField path="--replicas" type="string">
  Number of read replicas for the first branch
</ParamField>

<ParamField path="--region" type="string">
  Region to create the project in
</ParamField>

<ParamField path="--postgres-version" type="string">
  Please select the PostgreSQL version for this branch
</ParamField>

<ParamField path="--scale-to-zero-base" type="true | false">
  Default scale to zero status for base branches
</ParamField>

<ParamField path="--scale-to-zero-child" type="true | false">
  Default scale to zero status for child branches
</ParamField>

<ParamField path="--inactivity-period-base" type="15 | 30 | 60 | 120 | 180">
  Default inactivity period in minutes for base branches
</ParamField>

<ParamField path="--inactivity-period-child" type="15 | 30 | 60 | 120 | 180">
  Default inactivity period in minutes for child branches
</ParamField>

<ParamField path="--json" type="boolean" default="false">
  Output in JSON format
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

## delete

Delete a project

```bash theme={null}
xata project delete [--organization value] [--project value] [--json] [--yes] [--profile value] [--debug]
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--project" type="string">
  Project ID
</ParamField>

<ParamField path="--json" type="boolean" default="false">
  Output in JSON format
</ParamField>

<ParamField path="--yes" type="boolean" default="false">
  Do not ask for confirmation, assume yes.
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

## init

Link this folder to a project and branch

Writes the organization, project, branch and database to `.xata/` in this folder, so the commands run here no longer need them passed in.

```bash theme={null}
xata project init [--organization value] [--project value] [--branch value] [--database value] [--json] [--profile value] [--debug]
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--project" type="string">
  Project ID
</ParamField>

<ParamField path="--branch" type="string">
  Branch ID or name
</ParamField>

<ParamField path="--database" type="string">
  Database name on the branch
</ParamField>

<ParamField path="--json" type="boolean" default="false">
  Output in JSON format
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

**Examples:**

```bash theme={null}
# Link without prompts
xata project init --organization <org-id> --project <project-id> --branch <branch-id>
```

**Aliases:** `xata project connect`, `xata project switch`, `xata project link`

## get

Get a field from a project description

```bash theme={null}
xata project get [--organization value] [--project value] [--profile value] [--debug] <[project] field>...
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--project" type="string">
  Project ID
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

<ParamField path="[project] field" type="string">
  Project name and/or field to get
</ParamField>

## set

Set a field value for a project

The fields are name, scale-to-zero-base, scale-to-zero-child, inactivity-period-base and inactivity-period-child. The scale to zero and inactivity fields are the defaults new branches inherit, base for branches without a parent and child for the rest. Run it without a field to list them.

```bash theme={null}
xata project set [--organization value] [--project value] [--json] [--profile value] [--debug] [<field>] [<value>]
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--project" type="string">
  Project ID
</ParamField>

<ParamField path="--json" type="boolean" default="false">
  Output in JSON format
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

<ParamField path="field" type="string">
  The field to set
</ParamField>

<ParamField path="value" type="string">
  The value to set
</ParamField>

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
xata project ip-filter list [--organization value] [--project value] [--json] [--profile value] [--debug]
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--project" type="string">
  Project ID
</ParamField>

<ParamField path="--json" type="boolean" default="false">
  Output in JSON format
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

### ip-filter enable

Enable IP filtering for a project

```bash theme={null}
xata project ip-filter enable [--organization value] [--project value] [--json] [--profile value] [--debug]
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--project" type="string">
  Project ID
</ParamField>

<ParamField path="--json" type="boolean" default="false">
  Output in JSON format
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

### ip-filter disable

Disable IP filtering for a project

```bash theme={null}
xata project ip-filter disable [--organization value] [--project value] [--json] [--profile value] [--debug]
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--project" type="string">
  Project ID
</ParamField>

<ParamField path="--json" type="boolean" default="false">
  Output in JSON format
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

### ip-filter add

Add a CIDR entry to the IP filter allow list

```bash theme={null}
xata project ip-filter add [--organization value] [--project value] [--label value] [--json] [--profile value] [--debug] [<cidr>]
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--project" type="string">
  Project ID
</ParamField>

<ParamField path="--label" type="string">
  Label for the CIDR entry
</ParamField>

<ParamField path="--json" type="boolean" default="false">
  Output in JSON format
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

<ParamField path="cidr" type="string">
  IP address or CIDR block to add (e.g. 192.168.0.0/24 or 10.0.0.1)
</ParamField>

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
xata project ip-filter remove [--organization value] [--project value] [--force] [--json] [--profile value] [--debug] [<cidr>]
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--project" type="string">
  Project ID
</ParamField>

<ParamField path="--force" type="boolean" default="false">
  Skip confirmation prompts
</ParamField>

<ParamField path="--json" type="boolean" default="false">
  Output in JSON format
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

<ParamField path="cidr" type="string">
  CIDR block to remove
</ParamField>
