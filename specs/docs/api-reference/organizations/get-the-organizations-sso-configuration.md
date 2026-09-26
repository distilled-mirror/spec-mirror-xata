> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Get the organization's SSO configuration

> Lists the organization's identity providers and the email domains claimed for them. An organization signs in
through one provider per verified domain, so both are collections. Client secrets are write-only and are never
returned. An organization that has not set anything up yet responds 200 with empty lists.




## OpenAPI

````yaml https://api.xata.tech/openapi.json get /organizations/{organizationID}/sso
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
  /organizations/{organizationID}/sso:
    parameters:
      - $ref: '#/components/parameters/OrganizationIDParam'
    get:
      tags:
        - Organizations
      summary: Get the organization's SSO configuration
      description: >
        Lists the organization's identity providers and the email domains
        claimed for them. An organization signs in

        through one provider per verified domain, so both are collections.
        Client secrets are write-only and are never

        returned. An organization that has not set anything up yet responds 200
        with empty lists.
      operationId: getOrganizationSSO
      responses:
        '200':
          description: SSO configuration for the organization
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/OrganizationSSO'
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
    OrganizationSSO:
      description: >-
        An organization's single sign-on setup, as one identity provider per
        verified email domain.
      type: object
      properties:
        providers:
          description: >-
            Identity providers registered for this organization, at most one per
            verified domain.
          type: array
          items:
            $ref: '#/components/schemas/OrganizationSSOProvider'
        domains:
          description: Email domains claimed for this organization, verified and pending.
          type: array
          items:
            $ref: '#/components/schemas/OrganizationSSODomain'
      required:
        - providers
        - domains
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
    OrganizationSSODomain:
      description: An email domain claimed by an organization for SSO
      type: object
      properties:
        domain:
          description: The claimed email domain
          type: string
        provider_alias:
          description: >-
            The alias an identity provider on this domain will be given. Derived
            from the organization and the domain, so it is known before the
            provider exists, which is what lets a client show the redirect URI
            to register with the provider up front.
          type: string
        verified:
          description: Whether control of the domain has been proven through DNS
          type: boolean
        verification:
          description: >-
            The DNS record that proves control of the domain. Absent once the
            domain is verified.
          allOf:
            - $ref: '#/components/schemas/OrganizationSSODomainVerification'
          nullable: true
      required:
        - provider_alias
        - domain
        - verified
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
    OrganizationSSODomainVerification:
      description: The DNS TXT record that proves an organization controls a domain
      type: object
      properties:
        record_name:
          description: Name to create the record at
          type: string
        record_type:
          description: DNS record type
          type: string
        record_value:
          description: Value the record must hold
          type: string
      required:
        - record_name
        - record_type
        - record_value
  responses:
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
