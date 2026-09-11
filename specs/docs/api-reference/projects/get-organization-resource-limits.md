> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Get organization resource limits

> Retrieves the effective org-level limits, including project creation limits and branch configuration defaults. Project-specific overrides are not applied here.



## OpenAPI

````yaml https://api.xata.tech/openapi.json get /organizations/{organizationID}/limits
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
  /organizations/{organizationID}/limits:
    summary: Organization Limits
    description: >-
      Endpoint for retrieving the effective resource limits for an organization.
      Call this before creating a project or branch to know what constraints
      apply.
    get:
      tags:
        - Projects
      summary: Get organization resource limits
      description: >-
        Retrieves the effective org-level limits, including project creation
        limits and branch configuration defaults. Project-specific overrides are
        not applied here.
      operationId: getOrganizationLimits
      parameters:
        - name: organizationID
          in: path
          description: Unique identifier of the organization
          required: true
          schema:
            $ref: '#/components/schemas/OrganizationID'
      responses:
        '200':
          description: Effective resource limits for the organization
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/OrganizationLimits'
        '401':
          $ref: '#/components/responses/AuthorizationError'
        '403':
          $ref: '#/components/responses/AuthorizationError'
        5XX:
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
    OrganizationLimits:
      description: >-
        Effective resource limits for an organization, covering org-level
        defaults for all projects plus organization-specific constraints
      allOf:
        - $ref: '#/components/schemas/EffectiveProjectLimits'
        - type: object
          properties:
            maxProjects:
              description: Maximum number of projects allowed in the organization
              type: integer
              minimum: 1
              title: Maximum number of projects
            maxProjectsPerHour:
              description: >-
                Maximum number of projects that can be created in a rolling
                one-hour window
              type: integer
              minimum: 1
              title: Maximum project creation rate per hour
            maxBranchesPerOrg:
              description: >-
                Maximum number of active branches allowed across all projects in
                the organization
              type: integer
              minimum: 1
              title: Maximum number of branches per organization
          required:
            - maxProjects
            - maxProjectsPerHour
            - maxBranchesPerOrg
    EffectiveProjectLimits:
      description: Full set of resource limits applicable to a project and its branches
      type: object
      properties:
        maxDescriptionLength:
          description: Maximum character length allowed for branch descriptions
          type: integer
          minimum: 25
          title: Maximum length of a branch description
        maxBranchesPerProject:
          description: Maximum number of branches allowed per project
          type: integer
          title: Maximum number of branches per project
        maxInstancesPerBranch:
          description: Maximum number of database instances allowed per branch
          type: integer
          minimum: 1
          title: Maximum number of instances per branch
        minInstancesPerBranch:
          description: Minimum number of database instances required per branch
          type: integer
          minimum: 1
          title: Minimum number of instances per branch
        maxAllowedInstanceType:
          description: >-
            Maximum VCPUs (in millicores) allowed per instance; instance types
            with a higher VCPUsRequest are unavailable
          type: integer
          minimum: 1
          title: Maximum allowed instance type (VCPUs in millicores)
        maxBranchesPerHour:
          description: >-
            Maximum number of branches that can be created in a rolling one-hour
            window
          type: integer
          minimum: 1
          title: Maximum branch creation rate per hour
        maxStorageGBPerBranch:
          description: >-
            Maximum storage in GiB (gigabytes) allowed per branch; 0 means no
            limit
          type: integer
          minimum: 0
          title: Maximum storage per branch (GiB)
      required:
        - maxDescriptionLength
        - maxBranchesPerProject
        - maxInstancesPerBranch
        - minInstancesPerBranch
        - maxAllowedInstanceType
        - maxBranchesPerHour
        - maxStorageGBPerBranch
  responses:
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
