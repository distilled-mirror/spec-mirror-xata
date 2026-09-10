> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Serverless Proxy

> Connect to your Xata database via HTTP and WebSocket connections for serverless and edge environments.

The Serverless Proxy enables you to connect to your Xata PostgreSQL database using HTTP and WebSocket protocols, in addition to the standard PostgreSQL wire protocol (TCP). This is ideal for serverless functions and edge environments where maintaining persistent TCP connections is not practical.

<Info>
  Xata implements the [Neon serverless driver protocol](https://github.com/neondatabase/serverless), allowing you to use the `@neondatabase/serverless` npm package to connect to your Xata database over HTTP or WebSocket. If you're migrating from Neon, and you are using the serverless driver, you don't need any code changes.
</Info>

## Why use the Serverless Proxy?

Traditional PostgreSQL connections use TCP, which requires maintaining a persistent connection. This works well for long-running servers but presents challenges in serverless environments:

* **Cold starts**: Serverless functions spin up and down frequently, making persistent connections impractical
* **Edge locations**: Edge functions often have restrictions on outbound TCP connections
* **Connection limits**: Each serverless invocation could exhaust your database connections

The Serverless Proxy solves these problems by allowing stateless HTTP requests and efficient WebSocket connections to your database.

## Connection methods

Xata supports three ways to connect to your database:

| Method    | Protocol            | Best for                                       |
| --------- | ------------------- | ---------------------------------------------- |
| Standard  | TCP (wire protocol) | Traditional servers, long-running applications |
| HTTP      | HTTPS               | Serverless functions, one-off queries          |
| WebSocket | WSS                 | Edge functions, interactive applications       |

## Connection endpoints

When connecting to your Xata database, you can use different endpoints to route your traffic:

| Endpoint                            | Description                | Use case                                                              |
| ----------------------------------- | -------------------------- | --------------------------------------------------------------------- |
| `{branch-id}` (no suffix)           | Default endpoint           | Routes through PgBouncer for connection pooling                       |
| `{branch-id}-rw`                    | Read-write endpoint        | Routes directly to the primary instance for read and write operations |
| `{branch-id}-ro` or `{branch-id}-r` | Read-only endpoint         | Routes to read replicas for read operations only                      |
| `{branch-id}-pooler`                | Connection pooler endpoint | Routes through PgBouncer for connection pooling                       |

<Note>
  When connecting through the Serverless Proxy (HTTP and WebSocket), hostnames without an explicit endpoint suffix are routed through the PgBouncer connection pooler by default. To bypass the pooler and connect directly to the primary instance, use the `-rw` suffix.
</Note>

The connection pooler uses PgBouncer to manage database connections efficiently. This is particularly useful for applications that create many short-lived connections, as the pooler maintains a pool of persistent connections to the database and reuses them across client requests. Routing serverless traffic through the pooler by default keeps the number of connections to PostgreSQL low, even when many serverless functions invoke queries concurrently.

## Get started

Install the `@neondatabase/serverless` driver:

```bash theme={null}
npm install @neondatabase/serverless
```

Set the `DATABASE_URL` environment variable to your connection string. You can obtain it by running `xata branch url` or by copying the connection string from the Xata console:

```bash theme={null}
export DATABASE_URL=$(xata branch url)
```

## HTTP vs WebSocket

The driver supports two modes of communication, each suited to different use cases:

| Feature             | HTTP                           | WebSocket                 |
| ------------------- | ------------------------------ | ------------------------- |
| Connection overhead | None (stateless)               | One-time handshake        |
| Latency per query   | Higher (new request each time) | Lower (reuses connection) |
| Transactions        | Not supported                  | Supported                 |
| Session variables   | Not supported                  | Supported                 |
| Prepared statements | Not supported                  | Supported                 |
| Multiple queries    | One per request                | Batched efficiently       |
| Best for            | Single, simple queries         | Complex operations        |

**Use HTTP when:**

* You only need to run a single query per request
* Your queries are simple SELECTs or single INSERTs
* You want the simplest possible setup with zero connection management

**Use WebSocket when:**

* You need to run transactions (`BEGIN`/`COMMIT`/`ROLLBACK`)
* You're executing multiple queries in a single request
* You need session-level features like `SET` commands or prepared statements
* You want lower per-query latency for multiple operations

## HTTP connections

Use HTTP for simple, stateless queries. Each query is a single HTTP request—no connection management required.

```typescript theme={null}
import { neon } from '@neondatabase/serverless';

const sql = neon(process.env.DATABASE_URL);

const users = await sql`SELECT * FROM users WHERE id = ${userId}`;
```

<Note>
  HTTP connections are stateless. Each query runs in its own transaction and cannot share state with other queries. Use WebSocket connections if you need transactions or session-level features.
</Note>

### Error handling

#### Query timeout

HTTP queries have a 30-second timeout limit. If your query exceeds this limit, you'll receive a `504 Gateway Timeout` error with the code `QUERY_TIMEOUT`. For long-running queries, consider:

* Optimizing your query with indexes or query restructuring
* Breaking the operation into smaller chunks
* Using WebSocket connections for complex operations

#### Hibernated branches

If you attempt to connect to a hibernated branch, you'll receive a `409 Conflict` error with the code `BRANCH_HIBERNATED` and the message "branch is hibernated, reactivate it to continue". You'll need to manually reactivate the branch before connections can be established. Learn more about [Scale to Zero](/docs/core-concepts/scale-to-zero).

## WebSocket connections

Use WebSocket connections when you need transactions, session variables, or want to execute multiple queries efficiently. The WebSocket mode maintains a persistent connection, allowing for stateful interactions with the database.

<Note>
  If you are running in Node.js versions earlier than 21, you need to supply a WebSocket implementation. Install the `ws` package (`npm install ws`) and configure it before creating a pool:

  ```typescript theme={null}
  import { neonConfig } from '@neondatabase/serverless';
  import ws from 'ws';

  neonConfig.webSocketConstructor = ws;
  ```

  This is not needed in edge runtimes (Vercel Edge, Cloudflare Workers, Deno) or Node.js 21+, which have built-in WebSocket support.
</Note>

```typescript theme={null}
import { Pool } from '@neondatabase/serverless';

const pool = new Pool({ connectionString: process.env.DATABASE_URL });

const client = await pool.connect();
try {
  await client.query('BEGIN');
  await client.query('INSERT INTO orders (user_id) VALUES ($1)', [userId]);
  await client.query('UPDATE inventory SET quantity = quantity - 1 WHERE product_id = $1', [productId]);
  await client.query('COMMIT');
} catch (e) {
  await client.query('ROLLBACK');
  throw e;
} finally {
  client.release();
}
```

## Framework examples

<Tabs>
  <Tab title="Vercel">
    ```typescript theme={null}
    import { neon } from '@neondatabase/serverless';

    export async function GET() {
      const sql = neon(process.env.DATABASE_URL!);
      const users = await sql`SELECT * FROM users LIMIT 10`;
      return Response.json(users);
    }
    ```
  </Tab>

  <Tab title="Cloudflare Workers">
    ```typescript theme={null}
    import { neon } from '@neondatabase/serverless';

    export default {
      async fetch(request: Request, env: Env) {
        const sql = neon(env.DATABASE_URL);
        const users = await sql`SELECT * FROM users LIMIT 10`;
        return Response.json(users);
      },
    };
    ```
  </Tab>

  <Tab title="Netlify">
    ```typescript theme={null}
    import { neon } from '@neondatabase/serverless';
    import type { Context } from '@netlify/functions';

    export default async (request: Request, context: Context) => {
      const sql = neon(process.env.DATABASE_URL!);
      const users = await sql`SELECT * FROM users LIMIT 10`;
      return Response.json(users);
    };

    export const config = { path: '/api/users' };
    ```
  </Tab>

  <Tab title="AWS Lambda">
    ```typescript theme={null}
    import { neon } from '@neondatabase/serverless';

    export const handler = async (event) => {
      const sql = neon(process.env.DATABASE_URL);
      const users = await sql`SELECT * FROM users LIMIT 10`;
      return {
        statusCode: 200,
        body: JSON.stringify(users),
      };
    };
    ```
  </Tab>
</Tabs>
