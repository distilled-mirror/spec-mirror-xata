> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Get details of a specific invitation

> Retrieve detailed information about a specific invitation by its ID.



## OpenAPI

````yaml https://api.xata.tech/openapi.json get /organizations/{organizationID}/invitations/{invitationID}
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
  /organizations/{organizationID}/invitations/{invitationID}:
    parameters:
      - $ref: '#/components/parameters/OrganizationIDParam'
      - $ref: '#/components/parameters/InvitationIDParam'
    get:
      tags:
        - Organizations
      summary: Get details of a specific invitation
      description: Retrieve detailed information about a specific invitation by its ID.
      operationId: getOrganizationInvitation
      responses:
        '200':
          description: Invitation details retrieved successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/OrganizationInvitation'
        '400':
          $ref: '#/components/responses/BadRequestError'
        '401':
          $ref: '#/components/responses/AuthError'
        '403':
          $ref: '#/components/responses/AuthError'
        '404':
          $ref: '#/components/responses/SimpleError'
        5XX:
          description: Unexpected Error
      security:
        - xata:
            - invite:read
components:
  parameters:
    OrganizationIDParam:
      name: organizationID
      in: path
      required: true
      schema:
        $ref: '#/components/schemas/OrganizationID'
      description: Unique identifier for a specific organization
    InvitationIDParam:
      name: invitationID
      in: path
      required: true
      schema:
        type: string
      description: Unique identifier for an invitation
  schemas:
    OrganizationInvitation:
      type: object
      properties:
        id:
          description: Unique identifier for the invitation
          type: string
        organization_id:
          $ref: '#/components/schemas/OrganizationID'
          description: ID of the organization the invitation is for
        email:
          description: Email address of the invited user
          type: string
          format: email
        first_name:
          description: First name of the invited user
          type: string
          nullable: true
        last_name:
          description: Last name of the invited user
          type: string
          nullable: true
        created_at:
          description: Timestamp when the invitation was created
          type: string
          format: date-time
        expires_at:
          description: Timestamp when the invitation expires
          type: string
          format: date-time
        status:
          description: Current status of the invitation
          type: string
          enum:
            - pending
            - expired
      required:
        - id
        - organization_id
        - email
        - created_at
        - expires_at
        - status
    OrganizationID:
      title: OrganizationID
      type: string
      pattern: '[a-zA-Z0-9_-~:]+'
      x-oapi-codegen-extra-tags:
        validate: identifier
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
    SimpleError:
      description: Generic error response
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
