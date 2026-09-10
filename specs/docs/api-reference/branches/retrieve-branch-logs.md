> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Retrieve branch logs



## OpenAPI

````yaml https://api.xata.tech/openapi.json post /organizations/{organizationID}/projects/{projectID}/branches/{branchID}/logs
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
  /organizations/{organizationID}/projects/{projectID}/branches/{branchID}/logs:
    post:
      tags:
        - Branches
        - Logs
      summary: Retrieve branch logs
      operationId: branchLogs
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
              $ref: '#/components/schemas/BranchLogsRequest'
      responses:
        '200':
          description: Logs for a branch
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/BranchLogs'
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
            - logs:read
components:
  schemas:
    OrganizationID:
      title: OrganizationID
      type: string
      pattern: '[a-zA-Z0-9_-~:]+'
      x-oapi-codegen-extra-tags:
        validate: identifier
    BranchLogsRequest:
      type: object
      properties:
        start:
          description: Start time
          type: string
          format: date-time
        end:
          description: End time
          type: string
          format: date-time
        filters:
          description: >-
            Filters applied to log entries. Multiple filters are combined with
            AND.
          type: array
          items:
            $ref: '#/components/schemas/LogFilter'
        limit:
          type: integer
          default: 100
          maximum: 200
          minimum: 1
        cursor:
          description: Pagination cursor from a previous response
          type: string
      required:
        - start
        - end
    BranchLogs:
      description: A collection of logs for each of the instances of a branch
      type: object
      properties:
        start:
          type: string
          format: date-time
        end:
          type: string
          format: date-time
        logs:
          type: array
          items:
            $ref: '#/components/schemas/LogEntry'
        nextCursor:
          description: Pagination cursor for the next page
          type: string
          nullable: true
      required:
        - start
        - end
        - logs
        - nextCursor
    LogFilter:
      description: A single filter on log entries.
      type: object
      properties:
        field:
          description: Log attribute to filter on.
          type: string
          enum:
            - instance
            - level
            - process
            - body
        op:
          description: Match operator.
          type: string
          enum:
            - in
            - contains
            - icontains
            - regex
            - iregex
        values:
          description: 'Used with `op: in`.'
          type: array
          items:
            type: string
        value:
          description: Used with the body operators.
          type: string
      required:
        - field
        - op
    LogEntry:
      type: object
      properties:
        timestamp:
          type: string
          format: date-time
        instanceID:
          type: string
        level:
          $ref: '#/components/schemas/LogLevel'
        message:
          type: string
        process:
          description: Name of the PostgreSQL process that emitted the log
          type: string
      required:
        - timestamp
        - instanceID
        - message
    LogLevel:
      description: Log level enumeration
      type: string
      enum:
        - debug
        - info
        - warning
        - error
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
