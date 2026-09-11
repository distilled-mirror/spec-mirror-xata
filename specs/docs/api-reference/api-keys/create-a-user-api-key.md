> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Create a User API Key

> Create a new API key for the authenticated user.



## OpenAPI

````yaml https://api.xata.tech/openapi.json post /api-keys
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
  /api-keys:
    post:
      tags:
        - API Keys
      summary: Create a User API Key
      description: Create a new API key for the authenticated user.
      operationId: createUserAPIKey
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateAPIKeyRequest'
      responses:
        '201':
          description: API Key created successfully
          content:
            application/json:
              schema:
                type: object
                properties:
                  key:
                    $ref: '#/components/schemas/FullAPIKey'
                required:
                  - key
        '400':
          $ref: '#/components/responses/BadRequestError'
        '401':
          $ref: '#/components/responses/AuthError'
        5XX:
          description: Unexpected Error
      security:
        - xata:
            - keys:write
components:
  schemas:
    CreateAPIKeyRequest:
      type: object
      properties:
        name:
          type: string
        expiry:
          description: Expiration date for the API key, null for no expiry
          type: string
          format: date-time
          nullable: true
        scopes:
          description: Optional scopes assigned to the API key
          type: array
          items:
            type: string
          maxItems: 50
        projects:
          description: Limit access to these projects
          type: array
          items:
            type: string
          maxItems: 50
        branches:
          description: Limit access to these branches
          type: array
          items:
            type: string
          maxItems: 50
      required:
        - name
    FullAPIKey:
      type: object
      properties:
        id:
          type: string
        name:
          type: string
        preview:
          type: string
        scopes:
          type: array
          items:
            type: string
        projects:
          description: Projects this API key has access to
          type: array
          items:
            type: string
        branches:
          description: Branches this API key has access to
          type: array
          items:
            type: string
        created_at:
          type: string
          format: date-time
        expiry:
          description: Date when the API key expires (null if no expiry)
          type: string
          format: date-time
          nullable: true
        last_used:
          description: Timestamp of the last time the key was used (null if never)
          type: string
          format: date-time
          nullable: true
        token:
          description: The actual API key token
          type: string
        created_by:
          description: ID of the user that created this API key
          type: string
          nullable: true
        created_by_key:
          description: ID of the API key that created this API key
          type: string
          nullable: true
      required:
        - id
        - name
        - preview
        - created_at
        - expiry
        - last_used
        - token
        - scopes
        - projects
        - branches
  responses:
    BadRequestError:
      description: >-
        Error returned when the request is malformed or contains invalid
        parameters
      content:
        application/json:
          schema:
            type: object
            properties:
              id:
                description: Error identifier for tracking and debugging
                type: string
              message:
                description: Human-readable error message explaining the issue
                type: string
            required:
              - message
    AuthError:
      description: Error returned when authentication or authorization fails
      content:
        application/json:
          schema:
            type: object
            properties:
              id:
                description: Error identifier for tracking and debugging
                type: string
              message:
                description: Human-readable error message explaining the issue
                type: string
            example:
              message: invalid API key
            required:
              - message
  securitySchemes:
    xata:
      type: oauth2
      flows:
        implicit:
          authorizationUrl: https://auth.xata.io/realms/xata/protocol/openid-connect/auth
          scopes:
            org:read: Read organization information
            org:write: Create and modify organizations
            role:read: Read the roles held by organization members
            role:write: Change the role held by an organization member
            keys:read: Read API keys
            keys:write: Create and manage API keys
            project:read: Read project information
            project:write: Create and modify projects
            branch:read: Read branch information
            branch:write: Create and modify branches
            metrics:read: Read metrics data
            logs:read: Read logs data
            credentials:read: Read credentials
            credentials:write: Rotate credentials
            marketplace:write: Register with cloud marketplaces

````
