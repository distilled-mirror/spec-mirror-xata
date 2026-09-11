> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# List members of an organization



## OpenAPI

````yaml https://api.xata.tech/openapi.json get /organizations/{organizationID}/members
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
  /organizations/{organizationID}/members:
    parameters:
      - $ref: '#/components/parameters/OrganizationIDParam'
    get:
      tags:
        - Organizations
      summary: List members of an organization
      operationId: listOrganizationMembers
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                type: object
                properties:
                  members:
                    type: array
                    items:
                      $ref: '#/components/schemas/OrganizationMember'
                required:
                  - members
      security:
        - xata:
            - org:read
components:
  parameters:
    OrganizationIDParam:
      name: organizationID
      in: path
      required: true
      schema:
        $ref: '#/components/schemas/OrganizationID'
      description: Unique identifier for a specific organization
  schemas:
    OrganizationMember:
      description: A member of an organization and the role they hold in it
      allOf:
        - $ref: '#/components/schemas/UserWithID'
        - type: object
          properties:
            role:
              $ref: '#/components/schemas/OrganizationRoleName'
          required:
            - role
    OrganizationID:
      title: OrganizationID
      type: string
      pattern: '[a-zA-Z0-9_-~:]+'
      x-oapi-codegen-extra-tags:
        validate: identifier
    UserWithID:
      allOf:
        - $ref: '#/components/schemas/User'
        - type: object
          description: Extended user object that includes the unique user identifier
          properties:
            id:
              $ref: '#/components/schemas/UserID'
          required:
            - id
    OrganizationRoleName:
      description: The roles a member of an organization can hold
      type: string
      enum:
        - admin
        - editor
        - viewer
    User:
      description: User information including email, full name, and profile image
      type: object
      properties:
        email:
          description: Email address associated with the user account
          type: string
          format: email
        name:
          description: Name of the user
          type: string
      required:
        - name
        - email
    UserID:
      description: Unique identifier for a user account
      type: string
      pattern: '[a-zA-Z0-9_-~:]+'
      title: UserID
      x-oapi-codegen-extra-tags:
        validate: identifier
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
