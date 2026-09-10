> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Update project details

> Updates the details of a specific project by its ID, such as changing its name.



## OpenAPI

````yaml https://api.xata.tech/openapi.json patch /organizations/{organizationID}/projects/{projectID}
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
  /organizations/{organizationID}/projects/{projectID}:
    summary: Project Operations
    description: Endpoints for retrieving, updating, and deleting a specific project by ID.
    patch:
      tags:
        - Projects
      summary: Update project details
      description: >-
        Updates the details of a specific project by its ID, such as changing
        its name.
      operationId: updateProject
      parameters:
        - name: organizationID
          in: path
          description: Unique identifier of the organization containing the project
          required: true
          schema:
            $ref: '#/components/schemas/OrganizationID'
        - name: projectID
          in: path
          description: Unique identifier of the project to update
          required: true
          schema:
            type: string
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                name:
                  description: New name for the project
                  type: string
                configuration:
                  $ref: '#/components/schemas/UpdateProjectConfiguration'
      responses:
        '200':
          description: Project successfully updated
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Project'
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
            - project:write
components:
  schemas:
    OrganizationID:
      title: OrganizationID
      type: string
      pattern: '[a-zA-Z0-9_-~:]+'
      x-oapi-codegen-extra-tags:
        validate: identifier
    UpdateProjectConfiguration:
      description: Partial configuration update for a project
      type: object
      properties:
        scaleToZero:
          $ref: '#/components/schemas/ProjectScaleToZeroConfiguration'
        ipFiltering:
          $ref: '#/components/schemas/IPFilteringConfiguration'
    Project:
      description: >-
        Details of a project including its ID, name, and creation/update
        timestamps
      type: object
      properties:
        id:
          description: Unique identifier for the project
          type: string
        name:
          description: Human-readable name of the project
          type: string
        createdAt:
          description: Timestamp when the project was created
          type: string
          format: date-time
        updatedAt:
          description: Timestamp when the project was last updated
          type: string
          format: date-time
        configuration:
          $ref: '#/components/schemas/ProjectConfiguration'
      required:
        - id
        - name
        - createdAt
        - updatedAt
        - configuration
    ProjectScaleToZeroConfiguration:
      description: >-
        Whether the project branches are configured to scale down to zero when
        not in use
      type: object
      properties:
        baseBranches:
          $ref: '#/components/schemas/ScaleToZeroConfiguration'
        childBranches:
          $ref: '#/components/schemas/ScaleToZeroConfiguration'
      required:
        - baseBranches
        - childBranches
    IPFilteringConfiguration:
      description: Configuration for IP filtering on project branches
      type: object
      properties:
        enabled:
          description: Whether IP filtering is enabled
          type: boolean
        cidr:
          type: array
          items:
            $ref: '#/components/schemas/CidrEntry'
          maxItems: 64
      required:
        - enabled
        - cidr
    ProjectConfiguration:
      description: >-
        Configuration details for a project, including its scale to zero
        settings
      type: object
      properties:
        scaleToZero:
          $ref: '#/components/schemas/ProjectScaleToZeroConfiguration'
        ipFiltering:
          $ref: '#/components/schemas/IPFilteringConfiguration'
      required:
        - scaleToZero
    ScaleToZeroConfiguration:
      description: Configuration for scaling branches to zero when not in use
      type: object
      properties:
        enabled:
          description: Whether scale to zero is enabled
          type: boolean
        inactivityPeriodMinutes:
          description: >-
            Duration in minutes after which branches will be hibernated if not
            accessed
          type: integer
          default: 30
      required:
        - enabled
        - inactivityPeriodMinutes
    CidrEntry:
      type: object
      properties:
        cidr:
          description: IP address or CIDR block (e.g., "192.168.0.0/24")
          type: string
          maxLength: 64
          minLength: 1
        description:
          description: Optional label for the CIDR entry
          type: string
          maxLength: 100
      required:
        - cidr
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
