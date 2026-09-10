> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# API Keys

> Generate and manage API keys for secure access to your Xata database

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/platform/api-keys-nav.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=1879cbe2786539ab73a2d10a8385e684" alt="API Keys navigation overview" className="rounded-lg" width="2880" height="1556" data-path="images/platform/api-keys-nav.png" />

## Overview

API keys provide programmatic access to your Xata database without requiring user authentication. They're essential for integrating Xata with your applications and automating database operations.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/platform/api-keys-overview.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=a00b47903349d93ce17199850bf6ee31" alt="API Keys overview interface" className="rounded-lg" width="2032" height="630" data-path="images/platform/api-keys-overview.png" />

## Scoping

Control what each key can access with granular permissions and restrictions.

<img src="https://mintcdn.com/xata/PDDxPY9xptrEGBCP/images/platform/api-keys-scope.png?fit=max&auto=format&n=PDDxPY9xptrEGBCP&q=85&s=f62e1888cfc2cab30ab5fd4706aec250" alt="API Keys scoping interface" className="rounded-lg" width="2032" height="1934" data-path="images/platform/api-keys-scope.png" />

### Project and Branch Access

* **All projects and branches**: Full access across your organization
* **Specific branches**: Limited to selected development or production branches

### Functionality Access

Control access to specific platform features:

* **API keys**: Manage your API keys to authenticate requests to your organization
* **Organization**: Manage your organization settings and members
* **Organization deletion**: Submit a deletion request for an organization (`org:delete` scope)
* **Invitations**: Manage organization invitations to add new members
* **Project**: Manage your projects and their settings
* **Branch**: Manage your branches and their settings
* **Credentials**: Read and rotate database credentials for branches
* **Metrics**: View and manage your API usage metrics

Several CLI commands need more than branch access. See [Required scopes](/docs/cli#required-scopes) for which command needs which scope.

## Access Control

* **No Access**: Keys with no access cannot perform any operations
* **Read-Only**: Keys with read-only access can only read or view
* **Read and Write**: Keys with read and write access have full access

### From the CLI

Create and manage API keys using the Xata CLI:

```bash theme={null}
# Create a user API key
xata keys user create

# Create an organization API key
xata keys organization create

# List user API keys
xata keys user list

# List organization API keys
xata keys organization list
```

For more CLI commands, see the [CLI Reference](/docs/cli/keys).
