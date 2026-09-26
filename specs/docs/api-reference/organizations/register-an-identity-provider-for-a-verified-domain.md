> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Register an identity provider for a verified domain

> Registers an identity provider for one verified domain. `google` needs only the credentials and pins the login
to that Google Workspace domain; `oidc` is the fallback for any other provider and discovers its endpoints from
the issuer's `/.well-known/openid-configuration`. The domain must already be verified, and registering does not
redirect anyone on its own: that is enabled separately.




## OpenAPI

````yaml https://api.xata.tech/openapi.json post /organizations/{organizationID}/sso/providers
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
  /organizations/{organizationID}/sso/providers:
    parameters:
      - $ref: '#/components/parameters/OrganizationIDParam'
    post:
      tags:
        - Organizations
      summary: Register an identity provider for a verified domain
      description: >
        Registers an identity provider for one verified domain. `google` needs
        only the credentials and pins the login

        to that Google Workspace domain; `oidc` is the fallback for any other
        provider and discovers its endpoints from

        the issuer's `/.well-known/openid-configuration`. The domain must
        already be verified, and registering does not

        redirect anyone on its own: that is enabled separately.
      operationId: createOrganizationSSOProvider
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateOrganizationSSOProviderRequest'
      responses:
        '201':
          description: Identity provider registered
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/OrganizationSSOProvider'
        '400':
          $ref: '#/components/responses/BadRequestError'
        '401':
          $ref: '#/components/responses/AuthError'
        '403':
          description: >-
            Single sign-on is not enabled for the organization, or the caller is
            not an Admin
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AuthErrorResponse'
        '409':
          description: The domain is not verified, or already has an identity provider
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AuthErrorResponse'
        5XX:
          description: Unexpected Error
      security:
        - xata:
            - org:write
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
    CreateOrganizationSSOProviderRequest:
      description: >-
        Request payload for registering an identity provider for a verified
        domain
      type: object
      properties:
        type:
          $ref: '#/components/schemas/OrganizationSSOProviderType'
        domain:
          description: The verified email domain this provider will serve.
          type: string
        issuer:
          description: >-
            OIDC issuer URL. Required for `oidc`, and for `microsoft` where it
            names the Entra tenant; ignored for `google`. Must be https, and
            must serve a /.well-known/openid-configuration document naming
            itself as the issuer.
          type: string
          format: uri
        client_id:
          description: OAuth client ID the organization registered with the provider
          type: string
        client_secret:
          description: >-
            OAuth client secret. Write-only: it is stored in Keycloak and is
            never returned by this API.
          type: string
      required:
        - type
        - domain
        - client_id
        - client_secret
    OrganizationSSOProvider:
      description: >-
        An identity provider serving one of the organization's verified domains.
        The client secret is write-only and is never returned.
      type: object
      properties:
        alias:
          description: Stable identifier for this provider, used in its own endpoints.
          type: string
        type:
          $ref: '#/components/schemas/OrganizationSSOProviderType'
        display_name:
          description: Name members see for this provider when signing in.
          type: string
        domain:
          description: The verified email domain this provider serves.
          type: string
        enforced:
          description: >-
            Whether members on this domain are sent to this provider
            automatically, leaving no other way in. Registering a provider does
            not set this; it is enabled separately once a sign-in through it has
            worked.
          type: boolean
        issuer:
          description: OIDC issuer URL, without the /.well-known suffix.
          type: string
          format: uri
        client_id:
          description: OAuth client ID the organization registered with the provider.
          type: string
      required:
        - alias
        - type
        - display_name
        - domain
        - enforced
        - client_id
    AuthErrorResponse:
      description: Error response
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
    OrganizationID:
      title: OrganizationID
      type: string
      pattern: '[a-zA-Z0-9_-~:]+'
      x-oapi-codegen-extra-tags:
        validate: identifier
    OrganizationSSOProviderType:
      description: >-
        Which identity provider this is. `google` pins the login to a Google
        Workspace domain and `microsoft` to a single Entra tenant, taken from
        the issuer; `oidc` is the fallback for anything else that speaks OpenID
        Connect.
      type: string
      enum:
        - google
        - microsoft
        - oidc
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
