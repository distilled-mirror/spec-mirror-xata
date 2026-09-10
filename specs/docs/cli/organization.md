> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# organization

> Create, list, and manage organizations

An organization owns projects and their billing, and the people who can reach them. Members belong to it, invitations bring them in.

Every command below also takes `-h, --help`.

* [`xata organization list`](#list) — List all organizations
* [`xata organization describe`](#describe) — Describe an organization
* [`xata organization create`](#create) — Create a new organization
* [`xata organization delete`](#delete) — Delete an organization
* [`xata organization get`](#get) — Get a field from an organization description
* [`xata organization members`](#members) — Manage organization members
  * [`xata organization members list`](#members-list) — List all members of an organization
  * [`xata organization members invite`](#members-invite) — Send an invitation to join an organization
  * [`xata organization members remove`](#members-remove) — Remove a member from an organization
* [`xata organization invitations`](#invitations) — Manage organization invitations
  * [`xata organization invitations list`](#invitations-list) — List all invitations for an organization
  * [`xata organization invitations get`](#invitations-get) — Get details of a specific invitation
  * [`xata organization invitations create`](#invitations-create) — Create and send an invitation to join an organization
  * [`xata organization invitations delete`](#invitations-delete) — Delete an invitation
  * [`xata organization invitations resend`](#invitations-resend) — Resend an invitation

## list

List all organizations

```bash theme={null}
xata organization list [--json] [--profile value] [--debug]
```

<ParamField path="--json" type="boolean" default="false">
  Output in JSON format
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

**Aliases:** `xata organization ls`

## describe

Describe an organization

```bash theme={null}
xata organization describe [--organization value] [--json] [--profile value] [--debug]
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

**Aliases:** `xata organization view`, `xata organization show`

## create

Create a new organization

```bash theme={null}
xata organization create (--name value) [--json] [--profile value] [--debug]
```

<ParamField path="--name" type="string" required>
  Organization Name
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

Delete an organization

```bash theme={null}
xata organization delete [--organization value] [--json] [--yes] [--profile value] [--debug]
```

<ParamField path="--organization" type="string">
  Organization ID
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

## get

Get a field from an organization description

```bash theme={null}
xata organization get [--organization value] [--profile value] [--debug] <[organization] field>...
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

<ParamField path="[organization] field" type="string">
  Organization name and/or field to get
</ParamField>

## members

Manage organization members

### members list

List all members of an organization

```bash theme={null}
xata organization members list [--organization value] [--json] [--profile value] [--debug]
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

**Aliases:** `xata organization members ls`

### members invite

Send an invitation to join an organization

```bash theme={null}
xata organization members invite [--organization value] [--email value] [--json] [--profile value] [--debug]
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--email" type="string">
  Email address to invite
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

**Aliases:** `xata organization members add`

### members remove

Remove a member from an organization

```bash theme={null}
xata organization members remove [--organization value] [--user-id value] [--force] [--json] [--profile value] [--debug]
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--user-id" type="string">
  ID of the user to remove
</ParamField>

<ParamField path="--force" type="boolean" default="false">
  Skip confirmation prompt
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

**Aliases:** `xata organization members delete`, `xata organization members rm`

## invitations

Manage organization invitations

### invitations list

List all invitations for an organization

```bash theme={null}
xata organization invitations list [--organization value] [--status value] [--email value] [--search value] [--json] [--profile value] [--debug]
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--status" type="string">
  Filter by invitation status (pending or expired)
</ParamField>

<ParamField path="--email" type="string">
  Filter by email address
</ParamField>

<ParamField path="--search" type="string">
  Search invitations by email or name
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

**Aliases:** `xata organization invitations ls`

### invitations get

Get details of a specific invitation

```bash theme={null}
xata organization invitations get [--organization value] [--invitation-id value] [--json] [--profile value] [--debug]
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--invitation-id" type="string">
  ID of the invitation to view
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

**Aliases:** `xata organization invitations show`

### invitations create

Create and send an invitation to join an organization

```bash theme={null}
xata organization invitations create [--organization value] [--email value] [--json] [--profile value] [--debug]
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--email" type="string">
  Email address to invite
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

**Aliases:** `xata organization invitations add`, `xata organization invitations invite`

### invitations delete

Delete an invitation

```bash theme={null}
xata organization invitations delete [--organization value] [--invitation-id value] [--force] [--json] [--profile value] [--debug]
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--invitation-id" type="string">
  ID of the invitation to delete
</ParamField>

<ParamField path="--force" type="boolean" default="false">
  Skip confirmation prompt
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

**Aliases:** `xata organization invitations rm`, `xata organization invitations remove`

### invitations resend

Resend an invitation

```bash theme={null}
xata organization invitations resend [--organization value] [--invitation-id value] [--json] [--profile value] [--debug]
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--invitation-id" type="string">
  ID of the invitation to resend
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
