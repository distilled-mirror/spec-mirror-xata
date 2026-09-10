> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Get organization details

> Retrieve detailed information about a specific organization by its ID.



## OpenAPI

````yaml https://api.xata.tech/openapi.json get /organizations/{organizationID}
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
  /organizations/{organizationID}:
    summary: Organization Operations
    description: >-
      This endpoint provides operations for a specific organization, allowing
      retrieval, updating, and deletion of an organization by its unique
      identifier.
    parameters:
      - $ref: '#/components/parameters/OrganizationIDParam'
    get:
      tags:
        - Organizations
      summary: Get organization details
      description: Retrieve detailed information about a specific organization by its ID.
      operationId: getOrganization
      responses:
        '200':
          description: Organization details retrieved successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Organization'
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
    Organization:
      description: Organization details including ID and name
      type: object
      properties:
        id:
          $ref: '#/components/schemas/OrganizationID'
          description: Unique identifier for the organization
        name:
          description: Human-readable name of the organization
          type: string
        status:
          $ref: '#/components/schemas/OrganizationStatus'
          description: Current status of the organization
        marketplace:
          description: >-
            Marketplace provider for this organization (e.g. "aws"), if billed
            through a marketplace
          allOf:
            - $ref: '#/components/schemas/OrganizationMarketplaceProvider'
          nullable: true
      required:
        - id
        - name
        - status
    OrganizationID:
      title: OrganizationID
      type: string
      pattern: '[a-zA-Z0-9_-~:]+'
      x-oapi-codegen-extra-tags:
        validate: identifier
    OrganizationStatus:
      type: object
      properties:
        status:
          description: >-
            Indicates whether the organization is active, it's computed as
            `!disabled_by_admin AND billing_status == 'ok'`
          type: string
          enum:
            - enabled
            - disabled
        disabled_by_admin:
          description: Indicates if the organization has been disabled by an admin
          type: boolean
        admin_reason:
          description: Reason for the current admin status
          type: string
        billing_status:
          description: Indicates the status of the organization from a billing perspective
          type: string
          enum:
            - ok
            - no_payment_method
            - invoice_overdue
            - unknown
            - deletion_requested
        billing_reason:
          description: Reason for the current billing status
          type: string
        usage_tier:
          description: >-
            Usage tier of the organization. t1 is the default for new
            organizations, t2 is assigned when a valid payment method is on
            file.
          type: string
          enum:
            - t1
            - t2
        last_updated:
          description: Timestamp of the last update to the organization's status
          type: string
          format: date-time
        created_at:
          description: Timestamp when the organization was created
          type: string
          format: date-time
      required:
        - status
        - disabled_by_admin
        - billing_status
        - usage_tier
        - last_updated
    OrganizationMarketplaceProvider:
      description: Marketplace provider associated with an organization.
      type: string
      enum:
        - aws
      x-enum-varnames:
        - OrganizationMarketplaceProviderAWS
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
            group:read: Read organization groups and their members
            group:write: Create, modify, and delete organization groups and their members
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
