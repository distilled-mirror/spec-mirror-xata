> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Send an MCP message

> Handle a single JSON-RPC 2.0 message from an MCP client, per the MCP
[Streamable HTTP transport](https://modelcontextprotocol.io/specification/2025-06-18/basic/transports).

The server is stateless: every request is self-contained and carries the caller's
credential, so there is no session to establish or resume. `GET` and `DELETE` on this
path return `405 Method Not Allowed`.

**Requests** (messages with an `id`) are answered with the JSON-RPC response, either as
`application/json` or as a `text/event-stream` carrying the response and any notifications
emitted while it runs. Requests must accept both. **Notifications and responses**
(messages without an `id`) are answered with `202 Accepted` and an empty body.




## OpenAPI

````yaml https://api.xata.tech/openapi.json post /mcp
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
  - name: Vercel Resources
    x-displayName: Vercel Resources
  - name: Webhooks
    x-displayName: Webhooks
externalDocs:
  url: https://xata.io/docs/api
paths:
  /mcp:
    post:
      tags:
        - MCP
      summary: Send an MCP message
      description: >
        Handle a single JSON-RPC 2.0 message from an MCP client, per the MCP

        [Streamable HTTP
        transport](https://modelcontextprotocol.io/specification/2025-06-18/basic/transports).


        The server is stateless: every request is self-contained and carries the
        caller's

        credential, so there is no session to establish or resume. `GET` and
        `DELETE` on this

        path return `405 Method Not Allowed`.


        **Requests** (messages with an `id`) are answered with the JSON-RPC
        response, either as

        `application/json` or as a `text/event-stream` carrying the response and
        any notifications

        emitted while it runs. Requests must accept both. **Notifications and
        responses**

        (messages without an `id`) are answered with `202 Accepted` and an empty
        body.
      operationId: sendMcpRequest
      parameters:
        - name: Accept
          in: header
          description: Must list both `application/json` and `text/event-stream`.
          required: true
          schema:
            type: string
            default: application/json, text/event-stream
        - name: MCP-Protocol-Version
          in: header
          description: >
            Protocol revision negotiated during `initialize`, echoed on every
            subsequent request.

            Omitted on the `initialize` request itself.
          required: false
          schema:
            type: string
            example: '2025-06-18'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/JSONRPCMessage'
            examples:
              initialize:
                summary: Start a session and learn the server's capabilities
                value:
                  jsonrpc: '2.0'
                  id: 1
                  method: initialize
                  params:
                    protocolVersion: '2025-06-18'
                    capabilities: {}
                    clientInfo:
                      name: my-client
                      version: 1.0.0
              toolsList:
                summary: List the available tools
                value:
                  jsonrpc: '2.0'
                  id: 2
                  method: tools/list
              toolsCall:
                summary: Call a tool
                value:
                  jsonrpc: '2.0'
                  id: 3
                  method: tools/call
                  params:
                    name: search_operations
                    arguments:
                      query: list branches
      responses:
        '200':
          description: JSON-RPC response to the request
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/JSONRPCResponse'
              examples:
                toolsCall:
                  summary: Result of the tool call above
                  value:
                    jsonrpc: '2.0'
                    id: 3
                    result:
                      content:
                        - text: >-
                            {"operations":[{"operationID":"listBranches","method":"GET","path":"/organizations/{organizationID}/projects/{projectID}/branches","summary":"List
                            all branches","callTool":"call_read_operation"}]}
                          type: text
                      isError: false
            text/event-stream:
              schema:
                description: >-
                  Server-sent events, each `data` field holding one JSON-RPC
                  message.
                type: string
              example: >
                event: message

                data:
                {"jsonrpc":"2.0","id":3,"result":{"content":[{"type":"text","text":"{\"operations\":[{\"operationID\":\"listBranches\",\"method\":\"GET\",\"path\":\"/organizations/{organizationID}/projects/{projectID}/branches\",\"summary\":\"List
                all
                branches\",\"callTool\":\"call_read_operation\"}]}"}],"isError":false}}
        '202':
          description: Notification or response accepted; no body is returned
        '400':
          description: >-
            Malformed message, unsupported `MCP-Protocol-Version`, or an
            `Accept` header missing one of the required media types
        '401':
          description: Missing or invalid credentials
          headers:
            WWW-Authenticate:
              description: >-
                Bearer challenge pointing at the protected resource metadata,
                per RFC 9728.
              schema:
                type: string
                example: >-
                  Bearer
                  resource_metadata="https://api.xata.tech/.well-known/oauth-protected-resource/mcp"
        '413':
          description: Request body larger than 4 MiB
        '415':
          description: '`Content-Type` is not `application/json`'
        '500':
          description: Internal server error
      security:
        - apiKey: []
        - oidc: []
components:
  schemas:
    JSONRPCMessage:
      description: >
        A JSON-RPC 2.0 message. A request carries `id` and `method` and is
        answered with a

        response; a notification carries `method` without `id` and is not. The
        `method` and

        `params` values are defined by the MCP specification.
      type: object
      properties:
        jsonrpc:
          description: JSON-RPC protocol version. Always `2.0`.
          type: string
          enum:
            - '2.0'
        id:
          description: >-
            Request identifier, echoed on the response. Omitted for
            notifications.
          oneOf:
            - type: string
            - type: integer
        method:
          description: MCP method, such as `initialize`, `tools/list`, or `tools/call`.
          type: string
        params:
          description: Method parameters as defined by the MCP specification.
          type: object
          additionalProperties: true
      required:
        - jsonrpc
        - method
    JSONRPCResponse:
      description: A JSON-RPC 2.0 response. Exactly one of `result` or `error` is present.
      type: object
      properties:
        jsonrpc:
          description: JSON-RPC protocol version. Always `2.0`.
          type: string
          enum:
            - '2.0'
        id:
          description: Identifier of the request this responds to.
          oneOf:
            - type: string
            - type: integer
        result:
          description: Method result as defined by the MCP specification.
          type: object
          additionalProperties: true
        error:
          $ref: '#/components/schemas/JSONRPCError'
      required:
        - jsonrpc
        - id
    JSONRPCError:
      description: >-
        A JSON-RPC 2.0 error. Failures inside a tool are reported in the tool
        result with `isError`, not here.
      type: object
      properties:
        code:
          description: JSON-RPC error code.
          type: integer
        message:
          description: Short description of the error.
          type: string
        data:
          description: Additional error detail.
      required:
        - code
        - message
  securitySchemes:
    apiKey:
      type: apiKey
      in: header
      name: Authorization
      description: 'API key authentication using Bearer token format: Bearer <api_key>'
    oidc:
      type: openIdConnect
      openIdConnectUrl: https://auth.xata.io/realms/xata/.well-known/openid-configuration

````
