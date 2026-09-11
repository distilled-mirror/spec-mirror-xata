> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Rotate branch credentials

> Triggers a credential rotation for the branch database user. The new credentials can be retrieved via the credentials endpoint after rotation completes.



## OpenAPI

````yaml https://api.xata.tech/openapi.json post /organizations/{organizationID}/projects/{projectID}/branches/{branchID}/credentials/rotate
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
  /organizations/{organizationID}/projects/{projectID}/branches/{branchID}/credentials/rotate:
    post:
      tags:
        - Branches
      summary: Rotate branch credentials
      description: >-
        Triggers a credential rotation for the branch database user. The new
        credentials can be retrieved via the credentials endpoint after rotation
        completes.
      operationId: rotateBranchCredentials
      parameters:
        - name: organizationID
          in: path
          required: true
          schema:
            $ref: '#/components/schemas/OrganizationID'
        - name: projectID
          in: path
          required: true
          schema:
            type: string
        - name: branchID
          in: path
          required: true
          schema:
            type: string
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/RotateCredentialsRequest'
      responses:
        '204':
          description: Credential rotation initiated successfully
        '400':
          $ref: '#/components/responses/GenericError'
        '401':
          $ref: '#/components/responses/AuthorizationError'
        '404':
          $ref: '#/components/responses/GenericError'
        5XX:
          description: Unexpected Error
        default:
          description: Unexpected Error
      security:
        - xata:
            - credentials:write
components:
  schemas:
    OrganizationID:
      title: OrganizationID
      type: string
      pattern: '[a-zA-Z0-9_-~:]+'
      x-oapi-codegen-extra-tags:
        validate: identifier
    RotateCredentialsRequest:
      description: Request to rotate credentials for a branch database user
      type: object
      properties:
        username:
          description: Database username to rotate credentials for
          type: string
      required:
        - username
  responses:
    GenericError:
      description: Generic error response for most error conditions
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
    AuthorizationError:
      description: Error response when authentication or authorization fails
      content:
        application/json:
          schema:
            type: object
            properties:
              id:
                description: Error identifier for tracking and debugging
                type: string
              message:
                description: >-
                  Human-readable error message explaining the authentication or
                  authorization issue
                type: string
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
