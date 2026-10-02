> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Organization roles

> Choose who can manage your organization's members, billing and API keys

Every member of an [organization](/docs/platform/organization) is either an **Admin** or an **Editor**. Admins manage the organization itself. Editors work with its projects and branches.

The person who creates an organization is its first Admin.

<Note>
  Members who joined before roles were introduced are Admins. Change anyone who does not need to manage the organization to Editor.
</Note>

## Permissions

| Action | Admin | Editor |
| - | :-: | :-: |
| Create and manage projects and branches | Yes | Yes |
| Connect to and query databases | Yes | Yes |
| View the organization, its members and invitations | Yes | Yes |
| Invite and remove members | Yes | No |
| Change a member's role | Yes | No |
| Manage organization API keys | Yes | No |
| View and change billing | Yes | No |
| Configure [single sign-on](/docs/platform/sso) | Yes | No |
| Rename or delete the organization | Yes | No |

An Editor who attempts an Admin-only action through the API or CLI gets a `403 Forbidden` response.

## Invite a member

Select **Organization Settings** in the sidebar, then **Members**. Enter the email address, choose a role and select **Send Invitation**. New invitations default to **Editor**.

### From the CLI

```bash theme={null}
# Invite an Admin; omit --role to invite an Editor
xata organization members invite --email jane@example.com --role admin
```

## Change a member's role

On the **Members** page, pick a new role from the dropdown next to the member. The change applies immediately, without signing out.

An organization always keeps at least one Admin, so Xata refuses to demote or remove its last Admin. If you demote yourself, only another Admin can make you an Admin again.

### From the CLI

```bash theme={null}
# Find the member's user ID with: xata organization members list
xata organization members set-role --user-id <user-id> --role editor
```

### From the API

```bash theme={null}
curl -X PUT https://api.xata.tech/organizations/{organizationID}/members/{userID}/role \
  -H "Authorization: Bearer $XATA_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"role": "editor"}'
```

## API keys

A user API key acts with the current role of the member who created it, so an Editor's key cannot perform Admin-only actions whatever its scopes. Organization API keys act with Admin access, limited by their scopes. See [API Keys](/docs/platform/api-key).


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
