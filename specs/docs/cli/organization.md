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
  * [`xata organization members set-role`](#members-set-role) — Set the role of an organization member
* [`xata organization invitations`](#invitations) — Manage organization invitations
  * [`xata organization invitations list`](#invitations-list) — List all invitations for an organization
  * [`xata organization invitations get`](#invitations-get) — Get details of a specific invitation
  * [`xata organization invitations create`](#invitations-create) — Create and send an invitation to join an organization
  * [`xata organization invitations delete`](#invitations-delete) — Delete an invitation
  * [`xata organization invitations resend`](#invitations-resend) — Resend an invitation
* [`xata organization sso`](#sso) — Configure single sign-on for an organization
  * [`xata organization sso show`](#sso-show) — Show the domains and identity providers configured for an organization
  * [`xata organization sso domains`](#sso-domains) — Claim and verify the email domains that sign in through your identity providers
    * [`xata organization sso domains add`](#sso-domains-add) — Claim an email domain and print the DNS record that proves you own it
    * [`xata organization sso domains verify`](#sso-domains-verify) — Check the DNS record for a claimed domain and mark it verified
    * [`xata organization sso domains remove`](#sso-domains-remove) — Remove a claimed domain from an organization
  * [`xata organization sso providers`](#sso-providers) — Connect the identity providers that sign in each verified domain
    * [`xata organization sso providers add`](#sso-providers-add) — Connect an identity provider to a verified domain
    * [`xata organization sso providers remove`](#sso-providers-remove) — Disconnect an identity provider from an organization
    * [`xata organization sso providers enforce`](#sso-providers-enforce) — Require members on a domain to sign in through its identity provider
    * [`xata organization sso providers test`](#sso-providers-test) — Print a link that tests sign-in through an identity provider

## list

List all organizations

```bash theme={null}
xata organization list [--profile value] [--debug] [--json]
```

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**Aliases:** `xata organization ls`

## describe

Describe an organization

```bash theme={null}
xata organization describe [--organization value] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**Aliases:** `xata organization view`, `xata organization show`

## create

Create a new organization

```bash theme={null}
xata organization create (--name value) [--profile value] [--debug] [--json]
```

**--name** `string` *required*

Organization Name

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

## delete

Delete an organization

```bash theme={null}
xata organization delete [--organization value] [--yes] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--yes** `boolean` (default: false)

Do not ask for confirmation, assume yes.

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

## get

Get a field from an organization description

```bash theme={null}
xata organization get [--organization value] [--profile value] [--debug] [--json] <[organization] field>...
```

**--organization** `string`

Organization ID

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**\[organization] field** `string`

Organization name and/or field to get

## members

Manage organization members

### members list

List all members of an organization

```bash theme={null}
xata organization members list [--organization value] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**Aliases:** `xata organization members ls`

### members invite

Send an invitation to join an organization

```bash theme={null}
xata organization members invite [--organization value] [--email value] [--role admin|editor] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--email** `string`

Email address to invite

**--role** `admin | editor`

Role the new member holds once they accept

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**Aliases:** `xata organization members add`

### members remove

Remove a member from an organization

```bash theme={null}
xata organization members remove [--organization value] [--user-id value] [--force] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--user-id** `string`

ID of the user to remove

**--force** `boolean` (default: false)

Skip confirmation prompt

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**Aliases:** `xata organization members delete`, `xata organization members rm`

### members set-role

Set the role of an organization member

```bash theme={null}
xata organization members set-role [--organization value] [--user-id value] [--role admin|editor] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--user-id** `string`

ID of the member

**--role** `admin | editor`

Role to grant

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

## invitations

Manage organization invitations

### invitations list

List all invitations for an organization

```bash theme={null}
xata organization invitations list [--organization value] [--status value] [--email value] [--search value] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--status** `string`

Filter by invitation status (pending or expired)

**--email** `string`

Filter by email address

**--search** `string`

Search invitations by email or name

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**Aliases:** `xata organization invitations ls`

### invitations get

Get details of a specific invitation

```bash theme={null}
xata organization invitations get [--organization value] [--invitation-id value] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--invitation-id** `string`

ID of the invitation to view

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**Aliases:** `xata organization invitations show`

### invitations create

Create and send an invitation to join an organization

```bash theme={null}
xata organization invitations create [--organization value] [--email value] [--role admin|editor] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--email** `string`

Email address to invite

**--role** `admin | editor`

Role the new member holds once they accept

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**Aliases:** `xata organization invitations add`, `xata organization invitations invite`

### invitations delete

Delete an invitation

```bash theme={null}
xata organization invitations delete [--organization value] [--invitation-id value] [--force] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--invitation-id** `string`

ID of the invitation to delete

**--force** `boolean` (default: false)

Skip confirmation prompt

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**Aliases:** `xata organization invitations rm`, `xata organization invitations remove`

### invitations resend

Resend an invitation

```bash theme={null}
xata organization invitations resend [--organization value] [--invitation-id value] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--invitation-id** `string`

ID of the invitation to resend

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

## sso

Configure single sign-on for an organization

Claim an email domain, prove you own it with a DNS record, connect the identity provider its members sign in through, and then require it. Each verified domain has its own provider, so an organization can have several. See [https://xata.io/docs/platform/sso](https://xata.io/docs/platform/sso).

### sso show

Show the domains and identity providers configured for an organization

```bash theme={null}
xata organization sso show [--organization value] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**Aliases:** `xata organization sso get`

### sso domains

Claim and verify the email domains that sign in through your identity providers

#### sso domains add

Claim an email domain and print the DNS record that proves you own it

```bash theme={null}
xata organization sso domains add [--organization value] [--profile value] [--debug] [--json] [<domain>]
```

**--organization** `string`

Organization ID

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**domain** `string`

Domain to claim, such as acme.com

**Aliases:** `xata organization sso domains claim`

#### sso domains verify

Check the DNS record for a claimed domain and mark it verified

```bash theme={null}
xata organization sso domains verify [--organization value] [--profile value] [--debug] [--json] [<domain>]
```

**--organization** `string`

Organization ID

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**domain** `string`

Domain to verify, such as acme.com

#### sso domains remove

Remove a claimed domain from an organization

```bash theme={null}
xata organization sso domains remove [--organization value] [--yes] [--profile value] [--debug] [--json] [<domain>]
```

**--organization** `string`

Organization ID

**--yes** `boolean` (default: false)

Do not ask for confirmation, assume yes.

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**domain** `string`

Domain to remove, such as acme.com

**Aliases:** `xata organization sso domains delete`

### sso providers

Connect the identity providers that sign in each verified domain

#### sso providers add

Connect an identity provider to a verified domain

Reads the client secret from --client-secret, the XATA\_SSO\_CLIENT\_SECRET environment variable, or a prompt, so it need not appear in shell history. Provider types: google (Google Workspace), microsoft (Microsoft Entra ID), oidc (OpenID Connect). Every type except google needs --issuer-url.

```bash theme={null}
xata organization sso providers add [--organization value] [--type google|microsoft|oidc] [--domain value] [--issuer-url value] [--client-id value] [--client-secret value] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--type** `google | microsoft | oidc`

Provider type

**--domain** `string`

Verified domain this provider signs in

**--issuer-url** `string`

Issuer URL, required for every type except google

**--client-id** `string`

Client ID issued by the identity provider

**--client-secret** `string`

Client secret issued by the identity provider

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**Aliases:** `xata organization sso providers connect`

#### sso providers remove

Disconnect an identity provider from an organization

```bash theme={null}
xata organization sso providers remove [--organization value] [--yes] [--profile value] [--debug] [--json] [<alias>]
```

**--organization** `string`

Organization ID

**--yes** `boolean` (default: false)

Do not ask for confirmation, assume yes.

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**alias** `string`

Provider alias, as shown by xata organization sso show

**Aliases:** `xata organization sso providers delete`

#### sso providers enforce

Require members on a domain to sign in through its identity provider

Members on the domain lose the password form and the shared Google and GitHub buttons, so check the provider works with `xata organization sso providers test` before requiring it. Pass --disable to lift the requirement.

```bash theme={null}
xata organization sso providers enforce [--organization value] [--disable] [--yes] [--profile value] [--debug] [--json] [<alias>]
```

**--organization** `string`

Organization ID

**--disable** `boolean` (default: false)

Stop requiring SSO instead of requiring it

**--yes** `boolean` (default: false)

Do not ask for confirmation, assume yes.

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**alias** `string`

Provider alias, as shown by xata organization sso show

#### sso providers test

Print a link that tests sign-in through an identity provider

Members are only sent to a provider once SSO is required on its domain. The link signs in through the provider directly, so you can check it works before running `xata organization sso providers enforce`. Open it in a private window, since it signs that browser in as the account you test with.

```bash theme={null}
xata organization sso providers test [--organization value] [--profile value] [--debug] [--json] [<alias>]
```

**--organization** `string`

Organization ID

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**alias** `string`

Provider alias, as shown by xata organization sso show


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
