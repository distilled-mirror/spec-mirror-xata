> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Get branch details

> Retrieves detailed information about a specific branch by its ID, including status, connection string, and configuration.



## OpenAPI

````yaml https://api.xata.tech/openapi.json get /organizations/{organizationID}/projects/{projectID}/branches/{branchID}
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
  /organizations/{organizationID}/projects/{projectID}/branches/{branchID}:
    summary: Branch Operations
    description: Endpoints for retrieving, updating, and deleting a specific branch by ID.
    get:
      tags:
        - Branches
      summary: Get branch details
      description: >-
        Retrieves detailed information about a specific branch by its ID,
        including status, connection string, and configuration.
      operationId: describeBranch
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
          description: Unique identifier of the branch to retrieve details for
          required: true
          schema:
            type: string
      responses:
        '200':
          description: Branch details retrieved successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/BranchMetadata'
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
            - branch:read
components:
  schemas:
    OrganizationID:
      title: OrganizationID
      type: string
      pattern: '[a-zA-Z0-9_-~:]+'
      x-oapi-codegen-extra-tags:
        validate: identifier
    BranchMetadata:
      description: Detailed metadata about a branch, including its status and configuration
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
        region:
          description: Geographic region where the branch is deployed
          type: string
        status:
          $ref: '#/components/schemas/BranchStatus'
        connectionString:
          description: >-
            Deprecated: retrieve the connection string from the branch
            credentials endpoint (GET .../branches/{branchID}/credentials)
            instead. The hostname in this connection string carries a
            -deprecated marker in its first DNS label.
          type: string
          deprecated: true
          nullable: true
        publicAccess:
          description: Whether the branch allows public access without authentication
          type: boolean
        backupsEnabled:
          description: Whether the branch is in a region that supports backups
          type: boolean
        scaleToZero:
          $ref: '#/components/schemas/ScaleToZeroConfiguration'
        configuration:
          $ref: '#/components/schemas/ClusterConfiguration'
        backupConfiguration:
          $ref: '#/components/schemas/BackupConfiguration'
      required:
        - id
        - name
        - createdAt
        - updatedAt
        - region
        - status
        - connectionString
        - configuration
        - publicAccess
        - backupsEnabled
        - scaleToZero
    BranchStatus:
      description: >-
        Detailed status information about a branch and its underlying database
        cluster
      type: object
      properties:
        status:
          description: Status indicator for the branch according to CNPG
          type: string
          title: Current status of the cluster
        statusType:
          description: Type of status of the branch
          enum:
            - STATUS_TYPE_UNSPECIFIED
            - STATUS_TYPE_HEALTHY
            - STATUS_TYPE_TRANSIENT
            - STATUS_TYPE_FAULT
            - STATUS_TYPE_HIBERNATED
        message:
          description: Human-readable message explaining the current status
          type: string
          deprecated: true
          title: Summary of the cluster status
        lifecycle:
          $ref: '#/components/schemas/ClusterLifecycle'
        instanceCount:
          description: Total number of database instances in the cluster
          type: integer
          maximum: 5
          minimum: 1
          title: Number of instances in the cluster
        instanceReadyCount:
          description: Number of database instances that are ready and operational
          type: integer
          maximum: 5
          minimum: 0
          title: Number of instances ready in the cluster
        instances:
          description: Details about each individual database instance in the cluster
          type: array
          items:
            $ref: '#/components/schemas/InstanceStatus'
          title: List of instances in the cluster
      required:
        - status
        - statusType
        - instanceCount
        - instanceReadyCount
        - instances
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
    ClusterConfiguration:
      description: Configuration details for a database cluster backing a branch
      type: object
      properties:
        region:
          description: Geographic region where the cluster will be deployed
          type: string
        storage:
          description: Branch storage in GiB (gigabytes)
          type: integer
          format: int32
          maximum: 250
        instanceType:
          description: The instance type according to the xata instance types available
          type: string
        image:
          description: PostgreSQL image to use for the database instances
          type: string
        replicas:
          description: >-
            Number of replicas in the branch. Every child branch is created with
            no replicas. This can be updated.
          type: integer
          format: int32
          maximum: 4
          minimum: 0
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
      required:
        - image
        - region
        - instanceType
        - replicas
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
    ClusterLifecycle:
      description: Information about the current lifecycle state of a database cluster
      type: object
      properties:
        state:
          description: >-
            Current operational state of the cluster (ready, creating, updating,
            etc.)
          enum:
            - ready
            - creating
            - updating
            - upgrading
            - unknown
          title: Current state in the cluster lifecycle
        reason:
          description: >-
            Reason for the current lifecycle state, especially useful for
            non-ready states
          type: string
        phase:
          description: Current phase within the lifecycle process
          type: string
      deprecated: true
      required:
        - state
        - phase
    InstanceStatus:
      description: >-
        Status information about an individual database instance within a
        cluster
      type: object
      properties:
        id:
          description: Unique identifier for the database instance
          type: string
          title: Instance unique ID
        status:
          description: Current operational status of the instance
          type: string
          title: Current status of the instance
        primary:
          description: Indicates if this instance is currently the primary (write) instance
          type: boolean
          title: Whether the instance is the primary
        targetPrimary:
          description: >-
            Indicates if this instance is the target primary during a failover
            operation
          type: boolean
          title: Whether the cluster is switching and this is the target primary
      required:
        - id
        - status
        - primary
        - targetPrimary
      title: Status of an instance in the cluster
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
