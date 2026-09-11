> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Execute SQL query

> Execute a single SQL query or a batch of queries against a PostgreSQL branch.

**Authentication:** send the branch's PostgreSQL connection string in the
`Connection-String` header. The control-plane API key (Bearer token) is
**not** accepted on the gateway host.

**Routing:** the target branch, region, and endpoint type are taken from the
hostname embedded in the connection string, which must match the request host.
See the `Connection-String` security scheme for the host format.

**Single query:** provide `query` (and optional `params`) at the top level.

**Batch:** provide `queries` as an array of query objects, or send the request body
as a JSON array. Batch queries execute within a single transaction.




## OpenAPI

````yaml https://api.xata.tech/openapi.json post /sql
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
  /sql:
    servers:
      - url: https://{branch}.{region}.xata.tech
        description: Xata Gateway. The hostname encodes the target branch and region.
        variables:
          branch:
            default: my-branch
            description: Branch ID. Use the branch `id`, NOT its human-readable name
          region:
            default: us-east-1
            description: >-
              Branch region. The `us-east-1` default is a placeholder and may
              not be the region of your database.
    post:
      tags:
        - Gateway
      summary: Execute SQL query
      description: >
        Execute a single SQL query or a batch of queries against a PostgreSQL
        branch.


        **Authentication:** send the branch's PostgreSQL connection string in
        the

        `Connection-String` header. The control-plane API key (Bearer token) is

        **not** accepted on the gateway host.


        **Routing:** the target branch, region, and endpoint type are taken from
        the

        hostname embedded in the connection string, which must match the request
        host.

        See the `Connection-String` security scheme for the host format.


        **Single query:** provide `query` (and optional `params`) at the top
        level.


        **Batch:** provide `queries` as an array of query objects, or send the
        request body

        as a JSON array. Batch queries execute within a single transaction.
      operationId: query
      parameters:
        - name: Array-Mode
          in: header
          description: When `true`, return rows as arrays instead of objects.
          required: false
          schema:
            type: string
            enum:
              - 'true'
              - 'false'
        - name: Raw-Text-Output
          in: header
          description: When `true`, return all values as strings without type conversion.
          required: false
          schema:
            type: string
            enum:
              - 'true'
              - 'false'
        - name: Batch-Isolation-Level
          in: header
          description: Transaction isolation level for batch queries.
          required: false
          schema:
            type: string
            enum:
              - ReadCommitted
              - ReadUncommitted
              - RepeatableRead
              - Serializable
        - name: Batch-Read-Only
          in: header
          description: When `true`, execute the batch transaction in read-only mode.
          required: false
          schema:
            type: string
            enum:
              - 'true'
              - 'false'
        - name: Batch-Deferrable
          in: header
          description: When `true`, execute the batch transaction in deferrable mode.
          required: false
          schema:
            type: string
            enum:
              - 'true'
              - 'false'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/SQLRequest'
            examples:
              single:
                summary: Single parameterized query
                value:
                  query: SELECT id, name FROM users WHERE id = $1
                  params:
                    - 42
              batch:
                summary: Batch of queries in one transaction
                value:
                  queries:
                    - query: INSERT INTO logs (message) VALUES ($1)
                      params:
                        - hello
                    - query: SELECT count(*) FROM logs
      responses:
        '200':
          description: Query executed successfully
          content:
            application/json:
              schema:
                oneOf:
                  - $ref: '#/components/schemas/QueryResult'
                  - $ref: '#/components/schemas/BatchResponse'
              examples:
                single:
                  summary: Result of the single query above
                  value:
                    fields:
                      - name: id
                        tableID: 0
                        columnID: 0
                        dataTypeID: 23
                        dataTypeSize: 4
                        dataTypeModifier: -1
                        format: text
                      - name: name
                        tableID: 0
                        columnID: 0
                        dataTypeID: 25
                        dataTypeSize: -1
                        dataTypeModifier: -1
                        format: text
                    command: SELECT
                    rowCount: 1
                    rows:
                      - id: 42
                        name: Ada
                    rowAsArray: false
                batch:
                  summary: Result of the batch above, one entry per query
                  value:
                    results:
                      - command: INSERT
                        fields: []
                        rowCount: 1
                        rows: []
                        rowAsArray: false
                      - command: SELECT
                        fields:
                          - name: count
                            tableID: 0
                            columnID: 0
                            dataTypeID: 20
                            dataTypeSize: 8
                            dataTypeModifier: -1
                            format: text
                        rowCount: 1
                        rows:
                          - count: '1'
                        rowAsArray: false
        '400':
          description: >-
            Invalid request, SQL error, query execution timeout, or a result
            larger than the 10MB response limit
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '401':
          description: Missing or invalid connection string
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '403':
          description: Client IP is not allowed by the branch IP filter
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '404':
          description: Branch not found, it may have been deleted
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '409':
          description: Branch is hibernated, reactivate it before running queries
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '413':
          description: Request body is larger than 64MB
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '500':
          description: Internal server error
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '504':
          description: Timed out connecting to the database
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
      security:
        - branchConnectionString: []
components:
  schemas:
    SQLRequest:
      description: >-
        SQL query request. Provide either `query` for a single query or
        `queries` for a batch.
      type: object
      properties:
        query:
          description: SQL query to execute (single query mode).
          type: string
        params:
          description: Positional parameters for the query (`$1`, `$2`, ...).
          type: array
          items: {}
        queries:
          description: Array of queries for batch execution within a single transaction.
          type: array
          items:
            $ref: '#/components/schemas/QueryItem'
        arrayMode:
          description: Override array mode for this query (single query mode only).
          type: boolean
    QueryResult:
      description: Result of a single SQL query execution.
      type: object
      properties:
        fields:
          description: Column metadata for the result set.
          type: array
          items:
            $ref: '#/components/schemas/FieldDefinition'
        command:
          description: >-
            PostgreSQL command tag (e.g. `SELECT`, `INSERT`, `UPDATE`,
            `DELETE`).
          type: string
        rowCount:
          description: Number of rows affected by the command.
          type: integer
          nullable: true
        rows:
          description: >-
            Result rows. Each row is an object (column-name keys) or an array
            (when array mode is enabled).
          type: array
          items: {}
          x-go-type: any
        rowAsArray:
          description: Whether rows are returned as arrays (`true`) or objects (`false`).
          type: boolean
      required:
        - fields
        - command
        - rowCount
        - rows
        - rowAsArray
    BatchResponse:
      description: Response for a batch query execution.
      type: object
      properties:
        results:
          description: Results for each query in the batch, in order.
          type: array
          items:
            $ref: '#/components/schemas/QueryResult'
      required:
        - results
    ErrorResponse:
      description: Error response with PostgreSQL error fields.
      type: object
      properties:
        message:
          description: Human-readable error message.
          type: string
        code:
          description: PostgreSQL error code (SQLSTATE) or application error code.
          type: string
        severity:
          description: PostgreSQL error severity (e.g. `ERROR`, `FATAL`).
          type: string
        detail:
          description: Optional detail message.
          type: string
        hint:
          description: Optional hint for resolving the error.
          type: string
        position:
          description: Character position in the query where the error occurred.
          type: string
        internalPosition:
          description: Position in an internally-generated query.
          type: string
        internalQuery:
          description: Text of the internally-generated query.
          type: string
        where:
          description: Context in which the error occurred.
          type: string
        schema:
          description: Schema name related to the error.
          type: string
        table:
          description: Table name related to the error.
          type: string
        column:
          description: Column name related to the error.
          type: string
        dataType:
          description: Data type name related to the error.
          type: string
        constraint:
          description: Constraint name related to the error.
          type: string
        file:
          description: Source file where the error was reported (server-side).
          type: string
        line:
          description: Source line where the error was reported (server-side).
          type: string
        routine:
          description: Source routine where the error was reported (server-side).
          type: string
      required:
        - message
    QueryItem:
      description: A single query within a batch request.
      type: object
      properties:
        query:
          description: SQL query to execute.
          type: string
        params:
          description: Positional parameters for the query.
          type: array
          items: {}
        arrayMode:
          description: Override array mode for this individual query.
          type: boolean
      required:
        - query
    FieldDefinition:
      description: PostgreSQL column metadata from the row description message.
      type: object
      properties:
        name:
          description: Column name.
          type: string
        tableID:
          description: OID of the source table (0 if not a table column).
          type: integer
          format: int32
          x-go-type: uint32
        columnID:
          description: Attribute number of the column within the table.
          type: integer
          format: int32
          x-go-type: uint16
        dataTypeID:
          description: OID of the column data type.
          type: integer
          format: int32
          x-go-type: uint32
        dataTypeSize:
          description: Data type size (negative for variable-length types).
          type: integer
          format: int32
          x-go-type: int16
        dataTypeModifier:
          description: Type-specific modifier (e.g. precision/scale for numeric types).
          type: integer
          format: int32
        format:
          description: Data format (`text` or `binary`).
          type: string
      required:
        - name
        - tableID
        - columnID
        - dataTypeID
        - dataTypeSize
        - dataTypeModifier
        - format
  securitySchemes:
    branchConnectionString:
      type: apiKey
      in: header
      name: Connection-String
      description: >-
        Branch PostgreSQL connection string
        (`postgres://user:pass@{branch}.{region}.xata.tech/db`), including the
        embedded password. The hostname selects the target branch, region, and
        endpoint type (the `-rw`/`-ro` suffix, see `EndpointType`), and must
        match the request host. Obtain it from the Xata dashboard or the
        control-plane API. This is the only credential the gateway accepts; the
        control-plane API key (Bearer token) is rejected here. For the WebSocket
        endpoint (`GET /v2`) the same connection string is conveyed via the
        PostgreSQL startup message instead of this header.

````
