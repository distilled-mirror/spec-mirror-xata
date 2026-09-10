> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Organizations

> Collaborate with your team in a shared organization

## What is an Organization?

An organization is a hierarchical structure that supports multiple projects, each with their own database and branches. You can invite members to an organization and projects, and this is the entity you are billed on.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/platform/organization.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=8c6dc3416156fde7d5f3a46466b4114c" alt="Organization overview interface" className="rounded-lg" width="2880" height="1556" data-path="images/platform/organization.png" />

## Create an Organization

Set up your organization to start collaborating with your team and managing multiple projects.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/platform/organization-create.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=705b068133e492fb23ee12d09cda6ec1" alt="Create organization interface" className="rounded-lg" width="2880" height="1556" data-path="images/platform/organization-create.png" />

### From the CLI

Use the Xata CLI to create organizations programmatically:

```bash theme={null}
# Create a new organization
xata organization create --name "My Company"
```

For more CLI commands, see the [CLI Reference](/docs/cli/organization).

## Invite Members

Add team members to your organization by sending invitation emails.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/platform/organization-invite-members.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=07493f80faece810fe5632dd1163c90b" alt="Invite members interface" className="rounded-lg" width="2880" height="1556" data-path="images/platform/organization-invite-members.png" />

### Managing Invitations

You can manage organization invitations through the Console, CLI and API:

* **Send invitations**: Invite users by email to join your organization
* **List invitations**: View all pending and expired invitations with filtering options
* **View invitation details**: Get information about a specific invitation
* **Resend invitations**: Send a fresh invitation email with an extended expiration time
* **Delete invitations**: Cancel an existing invitation

## Organization API Keys

Manage API keys at the organization level for team-wide access and permissions.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/platform/organization-api-key.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=7a72f1c0648755b2d01f00e99a1ed23f" alt="Organization API keys interface" className="rounded-lg" width="2880" height="1556" data-path="images/platform/organization-api-key.png" />

Organization API keys provide centralized access control across all projects and branches within your organization. These keys can be scoped to specific projects, branches, or functionality levels.

For more detailed information about API key management, see [Account API Keys](/docs/platform/api-key).

## Billing

Manage billing and usage across your entire organization with centralized cost tracking and allocation.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/platform/organization-billing.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=c50d6e7bdc6b9aa115aa516f09aa9536" alt="Organization billing interface" className="rounded-lg" width="2880" height="1556" data-path="images/platform/organization-billing.png" />

Organization billing allows you to view both upcoming and historic invoices.

## Request Organization Deletion

You can submit a deletion request for an organization through the API. This flags the organization for deletion. Once accepted, the organization is immediately inaccessible.

```bash theme={null}
curl -X POST https://api.xata.tech/organizations/{organizationID}/deletion-request \
  -H "Authorization: Bearer $XATA_API_KEY"
```

A successful request returns `202 Accepted`.

<Note>
  The request is rejected with `409 Conflict` if the organization still has active projects or outstanding invoices. Delete or transfer all projects and settle outstanding invoices before requesting deletion.
</Note>

This endpoint requires an API key with the `org:delete` scope. See [API Keys](/docs/platform/api-key) for details on scoping.

For the immediate destructive variant, use the `DELETE /organizations/{organizationID}` endpoint, or the CLI:

```bash theme={null}
xata organization delete --organization <id>
```
