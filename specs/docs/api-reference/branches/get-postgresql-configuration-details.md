> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Get PostgreSQL configuration details

> Retrieves detailed information about the current PostgreSQL configuration parameters for a branch, including parameter types, descriptions, acceptable ranges, default values, and current values.



## OpenAPI

````yaml https://api.xata.tech/openapi.json get /organizations/{organizationID}/projects/{projectID}/branches/{branchID}/postgres-config
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
  /organizations/{organizationID}/projects/{projectID}/branches/{branchID}/postgres-config:
    get:
      tags:
        - Branches
      summary: Get PostgreSQL configuration details
      description: >-
        Retrieves detailed information about the current PostgreSQL
        configuration parameters for a branch, including parameter types,
        descriptions, acceptable ranges, default values, and current values.
      operationId: getBranchPostgresConfig
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
          description: >-
            Unique identifier of the branch to retrieve PostgreSQL configuration
            for
          required: true
          schema:
            type: string
      responses:
        '200':
          description: PostgreSQL configuration details retrieved successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/PostgresConfigDetails'
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
    PostgresConfigDetails:
      description: >-
        Detailed information about PostgreSQL configuration parameters for a
        branch
      type: object
      properties:
        parameters:
          description: >-
            Array of PostgreSQL configuration parameters with detailed
            information
          items:
            $ref: '#/components/schemas/PostgresConfigParameter'
          type: array
      required:
        - parameters
    PostgresConfigParameter:
      description: Detailed information about a single PostgreSQL configuration parameter
      type: object
      properties:
        name:
          description: The name of the PostgreSQL parameter
          type: string
        type:
          description: The data type of the parameter
          type: string
          enum:
            - string
            - int
            - float
            - bytes
            - enum
            - duration
            - boolean
        description:
          description: Human-readable description of what the parameter controls
          type: string
        section:
          description: The section/category this parameter belongs to
          type: string
        acceptableRange:
          description: Information about the acceptable range of values for this parameter
          type: object
          properties:
            minValue:
              description: Minimum allowed value (if applicable)
              type: string
            maxValue:
              description: Maximum allowed value (if applicable)
              type: string
            enumValues:
              description: List of allowed enum values (if applicable)
              type: array
              items:
                type: string
        defaultValue:
          description: The default value for this parameter
          type: string
        defaultValueSource:
          description: The source of the default value
          type: string
          enum:
            - postgres
            - instance_type
        currentValue:
          description: The current value of this parameter in the branch
          type: string
        documentationLink:
          description: Link to PostgreSQL documentation for this parameter
          type: string
        recommendation:
          description: Optional recommendation for this parameter (currently empty)
          type: string
        restartRequired:
          description: >-
            Whether a database restart is required for this parameter change to
            take effect
          type: boolean
      required:
        - name
        - type
        - description
        - defaultValue
        - defaultValueSource
        - currentValue
        - documentationLink
        - recommendation
        - section
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
