> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# keys

> Create, list, and delete API keys

A user key belongs to the account that created it, an organization key to an organization. Both are printed once, when they are created.

Every command below also takes `-h, --help`.

* [`xata keys user`](#user) — Manage API keys for the current user
  * [`xata keys user list`](#user-list) — List all API keys of the current user
  * [`xata keys user create`](#user-create) — Create a new API key
  * [`xata keys user delete`](#user-delete) — Delete one or more API keys
* [`xata keys organization`](#organization) — Manage API keys for an organization
  * [`xata keys organization list`](#organization-list) — List all API keys for an organization
  * [`xata keys organization create`](#organization-create) — Create a new API key
  * [`xata keys organization delete`](#organization-delete) — Delete one or more API keys

## user

Manage API keys for the current user

### user list

List all API keys of the current user

```bash theme={null}
xata keys user list [--json] [--profile value] [--debug]
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

**Aliases:** `xata keys user ls`

### user create

Create a new API key

Creates a key for the account you are logged in with. It is printed once, when it is created, and cannot be read again.

```bash theme={null}
xata keys user create [--name value] [--expiry value] [--json] [--profile value] [--debug]
```

<ParamField path="--name" type="string">
  API key name
</ParamField>

<ParamField path="--expiry" type="string">
  Expiry, as a date or a phrase such as 'in 1 week', or 'never'
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
# Create a key that does not expire
xata keys user create --name ci
# Create a key that expires
xata keys user create --name ci --expiry 2026-12-31
```

### user delete

Delete one or more API keys

```bash theme={null}
xata keys user delete [--json] [--profile value] [--debug] <keyId>...
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

<ParamField path="keyId" type="string">
  IDs of the keys to delete
</ParamField>

## organization

Manage API keys for an organization

**Aliases:** `xata keys org`

### organization list

List all API keys for an organization

```bash theme={null}
xata keys organization list [--organization value] [--json] [--profile value] [--debug]
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

**Aliases:** `xata keys organization ls`

### organization create

Create a new API key

Creates a key for an organization rather than for an account. It is printed once, when it is created, and cannot be read again.

```bash theme={null}
xata keys organization create [--organization value] [--name value] [--expiry value] [--json] [--profile value] [--debug]
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--name" type="string">
  API key name
</ParamField>

<ParamField path="--expiry" type="string">
  Expiry, as a date or a phrase such as 'in 1 week', or 'never'
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
# Create a key for an organization
xata keys organization create --name deploy
```

### organization delete

Delete one or more API keys

```bash theme={null}
xata keys organization delete [--organization value] [--json] [--profile value] [--debug] <keyId>...
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

<ParamField path="keyId" type="string">
  IDs of the keys to delete
</ParamField>
