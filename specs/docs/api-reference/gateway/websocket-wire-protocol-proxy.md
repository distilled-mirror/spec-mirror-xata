> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# WebSocket wire protocol proxy

> Upgrade to a WebSocket connection that proxies the PostgreSQL wire protocol.
The client sends and receives PostgreSQL protocol messages over binary WebSocket frames.

**Authentication:** the same branch connection string credential as the
HTTP SQL endpoint is used, but it is supplied via the PostgreSQL startup
message inside the wire protocol rather than an HTTP header. The
control-plane API key (Bearer token) is not accepted.




## OpenAPI

````yaml https://api.xata.tech/openapi.json get /v2
openapi: 3.0.0
info:
  title: Xata API
  description: Xata API
  version: '1.0'
  contact:
    name: help@xata.io
servers:
  - url: https://api.xata.tech
    description: Xata API
security: []
tags:
  - name: Organizations
    description: Operations for creating, retrieving, updating, and deleting organizations
    x-displayName: Organizations
  - name: Users
    description: Operations for managing user accounts and profiles
    x-displayName: Users
  - name: API Keys
    description: >-
      Operations for managing API keys, including creation, listing, and
      deletion
    x-displayName: API Keys
  - name: Marketplace
    description: Operations for linking user accounts to cloud marketplace subscriptions
    x-displayName: Marketplace
  - name: Billing
    description: Internal organization billing operations
    x-displayName: Billing
  - name: Gateway
    description: >-
      PostgreSQL connectivity via HTTP SQL, WebSocket wire protocol proxy, and
      native wire protocol.
    x-displayName: Gateway
  - name: MCP
    description: Model Context Protocol endpoint.
    x-displayName: MCP
  - name: Projects Webhooks
    x-displayName: Projects Webhooks
  - name: Projects
    description: >-
      Operations for creating, retrieving, updating, and deleting projects
      within an organization
    x-displayName: Projects
  - name: Branches
    description: >-
      Operations for managing database branches within projects, including
      creation, configuration, and deletion
    x-displayName: Branches
  - name: GitHub App
    description: Operations for managing GitHub App installation mappings
    x-displayName: GitHub App
  - name: Metrics
    description: Operations for retrieving observability metrics for a branch
    x-displayName: Metrics
  - name: Logs
    description: Operations for retrieving log entries for a branch
    x-displayName: Logs
  - name: Vercel
    x-displayName: Vercel
  - name: Webhooks
    x-displayName: Webhooks
externalDocs:
  url: https://xata.io/docs/api
paths:
  /v2:
    servers:
      - url: https://{branch}.{region}.xata.tech
        description: Xata Gateway. The hostname encodes the target branch and region.
        variables:
          branch:
            default: my-branch
            description: Branch ID. Use the branch `id`, NOT its human-readable name
          region:
            default: us-east-1
            description: >-
              Branch region. The `us-east-1` default is a placeholder and may
              not be the region of your database.
    get:
      tags:
        - Gateway
      summary: WebSocket wire protocol proxy
      description: >
        Upgrade to a WebSocket connection that proxies the PostgreSQL wire
        protocol.

        The client sends and receives PostgreSQL protocol messages over binary
        WebSocket frames.


        **Authentication:** the same branch connection string credential as the

        HTTP SQL endpoint is used, but it is supplied via the PostgreSQL startup

        message inside the wire protocol rather than an HTTP header. The

        control-plane API key (Bearer token) is not accepted.
      operationId: websocket
      responses:
        '101':
          description: Switching Protocols — WebSocket connection established
        '400':
          description: Bad request — unable to upgrade connection

````
