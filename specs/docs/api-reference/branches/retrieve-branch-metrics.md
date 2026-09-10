> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Retrieve branch metrics

> Returns time-series data for one or more metrics of a branch.



## OpenAPI

````yaml https://api.xata.tech/openapi.json post /organizations/{organizationID}/projects/{projectID}/branches/{branchID}/metrics
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
  /organizations/{organizationID}/projects/{projectID}/branches/{branchID}/metrics:
    post:
      tags:
        - Branches
        - Metrics
      summary: Retrieve branch metrics
      description: Returns time-series data for one or more metrics of a branch.
      operationId: branchMetrics
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
              $ref: '#/components/schemas/BranchMetricsRequest'
      responses:
        '200':
          description: Metrics for a branch
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/BranchMetrics'
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
            - metrics:read
components:
  schemas:
    OrganizationID:
      title: OrganizationID
      type: string
      pattern: '[a-zA-Z0-9_-~:]+'
      x-oapi-codegen-extra-tags:
        validate: identifier
    BranchMetricsRequest:
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
        metrics:
          description: List of metric names to query.
          type: array
          items:
            $ref: '#/components/schemas/BranchMetricName'
          maxItems: 15
          minItems: 1
        instances:
          description: List of instance IDs to query
          type: array
          items:
            type: string
        aggregations:
          description: >-
            List of aggregations to get, this is how the data-points within the
            interval are aggregated. Each one will generate a separate
            time-series per metric in the response.
          type: array
          items:
            type: string
            enum:
              - avg
              - max
              - min
      required:
        - start
        - end
        - metrics
        - aggregations
    BranchMetrics:
      description: >-
        A collection of metrics (cpu, memory, disk,...) for each of the
        instances of a branch
      type: object
      properties:
        start:
          type: string
          format: date-time
        end:
          type: string
          format: date-time
        results:
          description: >-
            One entry per requested metric, in the order the metrics were
            requested.
          type: array
          items:
            $ref: '#/components/schemas/BranchMetricResult'
      required:
        - start
        - end
        - results
    BranchMetricName:
      description: Name of a branch metric exposed by the API.
      type: string
      enum:
        - cpu
        - memory
        - disk
        - connections_active
        - connections_idle
        - network_ingress
        - network_egress
        - iops_read
        - iops_write
        - latency_read
        - latency_write
        - throughput_read
        - throughput_write
        - wal_sync_time
        - replication_lag_time
    BranchMetricResult:
      description: Time-series for a single metric.
      type: object
      properties:
        metric:
          description: Name of the queried metric.
          type: string
        unit:
          description: Unit of the metric (percentage, bytes, ms, etc.)
          type: string
        series:
          type: array
          items:
            $ref: '#/components/schemas/MetricSeries'
      required:
        - metric
        - unit
        - series
    MetricSeries:
      description: The metric series
      type: object
      properties:
        instanceID:
          description: ID of the instance
          type: string
        aggregation:
          description: The aggregation used to generate this time-series
          type: string
          enum:
            - avg
            - max
            - min
        values:
          type: array
          items:
            type: object
            properties:
              timestamp:
                type: string
                format: date-time
              value:
                type: number
            required:
              - timestamp
              - value
      required:
        - instanceID
        - aggregation
        - values
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
