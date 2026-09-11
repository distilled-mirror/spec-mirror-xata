> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Update branch details

> Updates the configuration of a specific branch by its ID, such as changing its name, description, or resource allocation.



## OpenAPI

````yaml https://api.xata.tech/openapi.json patch /organizations/{organizationID}/projects/{projectID}/branches/{branchID}
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
  /organizations/{organizationID}/projects/{projectID}/branches/{branchID}:
    summary: Branch Operations
    description: Endpoints for retrieving, updating, and deleting a specific branch by ID.
    patch:
      tags:
        - Branches
      summary: Update branch details
      description: >-
        Updates the configuration of a specific branch by its ID, such as
        changing its name, description, or resource allocation.
      operationId: updateBranch
      parameters:
        - name: organizationID
          in: path
          description: Unique identifier of the organization containing the project
          required: true
          schema:
            $ref: '#/components/schemas/OrganizationID'
        - name: projectID
          in: path
          description: Unique identifier of the project containing the branch
          required: true
          schema:
            type: string
        - name: branchID
          in: path
          description: Unique identifier of the branch to update
          required: true
          schema:
            type: string
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/BranchUpdateDetails'
      responses:
        '200':
          description: Branch successfully updated
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/BranchShortMetadata'
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
            - branch:write
components:
  schemas:
    OrganizationID:
      title: OrganizationID
      type: string
      pattern: '[a-zA-Z0-9_-~:]+'
      x-oapi-codegen-extra-tags:
        validate: identifier
    BranchUpdateDetails:
      description: Details that can be updated for an existing branch
      type: object
      properties:
        name:
          description: New name for the branch
          type: string
        description:
          description: New description for the branch. Send an empty string to clear it.
          type: string
          maxLength: 255
          pattern: '^([a-zA-Z0-9][a-zA-Z0-9\-_./: ]*)?$'
        replicas:
          description: Number of database replicas to scale to
          type: integer
          format: int32
          maximum: 4
          minimum: 0
        storage:
          description: >-
            Branch storage in GiB (gigabytes). The maximum allowed value depends
            on the organization's storage limit.
          type: integer
          format: int32
          minimum: 1
        instanceType:
          description: New instance type for the branch
          type: string
        backupConfiguration:
          $ref: '#/components/schemas/BackupConfiguration'
        hibernate:
          description: >-
            Enabled when the branch should be hibernated, disabled if it needs
            to be reactivated.
          type: boolean
        scaleToZero:
          $ref: '#/components/schemas/ScaleToZeroConfiguration'
        postgresConfigurationParameters:
          description: Arbitrary PostgreSQL configuration parameters for the cluster
          type: object
          additionalProperties:
            type: string
        preloadLibraries:
          description: List of PostgreSQL extensions and libraries to preload
          type: array
          items:
            type: string
        image:
          description: PostgreSQL image to use for the database instances
          type: string
    BranchShortMetadata:
      description: >-
        Basic metadata about a branch, used in response to create/update
        operations
      type: object
      properties:
        id:
          description: Unique identifier for the branch
          type: string
        name:
          description: Human-readable name of the branch
          type: string
        description:
          description: Optional description of the branch purpose or contents
          type: string
        createdAt:
          description: Timestamp when the branch was created
          type: string
          format: date-time
        updatedAt:
          description: Timestamp when the branch was last updated
          type: string
          format: date-time
        parentID:
          description: >-
            Identifier of the parent branch if this is a derived branch, null
            otherwise
          type: string
          nullable: true
        connectionString:
          description: >-
            Deprecated: retrieve the connection string from the branch
            credentials endpoint (GET .../branches/{branchID}/credentials)
            instead. The hostname in this connection string carries a
            -deprecated marker in its first DNS label.
          type: string
          deprecated: true
          nullable: true
        region:
          description: Geographic region where the branch is deployed
          type: string
        publicAccess:
          description: Whether the branch allows public access without authentication
          type: boolean
      required:
        - id
        - name
        - createdAt
        - updatedAt
        - region
        - publicAccess
    BackupConfiguration:
      description: Details about the branch continuous backup configuration
      type: object
      properties:
        retentionPeriod:
          description: how long are we keeping the backups around for
          type: integer
          format: int32
          default: 2
          maximum: 35
          minimum: 2
        backupTime:
          description: time of day/week when we are taking a full backup
          type: string
          pattern: ^(\*|[0-6]):(0[0-9]|1[0-9]|2[0-3]):([0-5][0-9])$
      x-excluded: true
      x-internal: true
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
