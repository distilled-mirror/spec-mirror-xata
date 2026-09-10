> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Get available instance types

> Retrieves a list of all instance types for the specified organization and region



## OpenAPI

````yaml https://api.xata.tech/openapi.json get /organizations/{organizationID}/instanceTypes
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
  /organizations/{organizationID}/instanceTypes:
    summary: Instance Types Operations
    description: >-
      Endpoints for retrieving available instance types for creating branches
      within an organization.
    get:
      tags:
        - Projects
      summary: Get available instance types
      description: >-
        Retrieves a list of all instance types for the specified organization
        and region
      operationId: listInstanceTypes
      parameters:
        - name: organizationID
          in: path
          description: >-
            Unique identifier of the organization to check instance type
            availability for
          required: true
          schema:
            $ref: '#/components/schemas/OrganizationID'
        - name: region
          in: query
          description: Region to check instance type availability for
          required: true
          schema:
            type: string
      responses:
        '200':
          description: List of instance types available for the organization
          content:
            application/json:
              schema:
                type: object
                properties:
                  instanceTypes:
                    description: Array of available instance types with their properties
                    type: array
                    items:
                      type: object
                      properties:
                        name:
                          description: Instance type name
                          type: string
                        vcpus:
                          description: number of vCPUs
                          type: integer
                        ram:
                          description: memory in Gi
                          type: integer
                        hourlyRate:
                          description: price per hour for this instance type and region
                          type: number
                          format: double
                        storageMonthlyRate:
                          description: price per month for a GB of storage for this region
                          type: number
                          format: double
                        region:
                          description: region where this instance type is available
                          type: string
                      required:
                        - name
                        - vcpus
                        - ram
                        - hourlyRate
                        - region
                        - storageMonthlyRate
                required:
                  - instanceTypes
        '400':
          $ref: '#/components/responses/GenericError'
        '401':
          $ref: '#/components/responses/AuthorizationError'
        5XX:
          description: Unexpected Error
        default:
          description: Unexpected Error
      security:
        - xata:
            - org:read
components:
  schemas:
    OrganizationID:
      title: OrganizationID
      type: string
      pattern: '[a-zA-Z0-9_-~:]+'
      x-oapi-codegen-extra-tags:
        validate: identifier
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
