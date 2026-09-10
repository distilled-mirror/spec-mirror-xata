> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Use Xata with Drizzle

> Learn how to connect your Drizzle ORM project to a Xata PostgreSQL database.

[Drizzle ORM](https://orm.drizzle.team/) is a TypeScript-first ORM that provides type-safe database access with a SQL-like query builder. You can use Drizzle with Xata using either the standard PostgreSQL driver or the serverless driver for edge environments.

## Prerequisites

* Xata account with a project and branch
* Node.js project with TypeScript

## Installation

Install Drizzle ORM and the PostgreSQL driver:

<Tabs>
  <Tab title="Standard (TCP)">
    ```bash theme={null}
    npm install drizzle-orm postgres
    npm install -D drizzle-kit
    ```
  </Tab>

  <Tab title="Serverless (HTTP/WebSocket)">
    ```bash theme={null}
    npm install drizzle-orm @neondatabase/serverless
    npm install -D drizzle-kit
    ```
  </Tab>
</Tabs>

## Get your connection string

```bash theme={null}
xata branch url
```

Set it as an environment variable:

```bash theme={null}
DATABASE_URL=postgresql://user:password@host:port/database
```

## Connect to Xata

<Tabs>
  <Tab title="Standard (TCP)">
    Use this for traditional servers and long-running applications:

    ```typescript theme={null}
    import { drizzle } from 'drizzle-orm/postgres-js';
    import postgres from 'postgres';

    const client = postgres(process.env.DATABASE_URL!);
    export const db = drizzle(client);
    ```
  </Tab>

  <Tab title="Serverless (HTTP)">
    Use this for serverless functions with single queries:

    ```typescript theme={null}
    import { drizzle } from 'drizzle-orm/neon-http';
    import { neon } from '@neondatabase/serverless';

    const client = neon(process.env.DATABASE_URL!);
    export const db = drizzle(client);
    ```
  </Tab>

  <Tab title="Serverless (WebSocket)">
    Use this for serverless functions that need transactions:

    ```typescript theme={null}
    import { drizzle } from 'drizzle-orm/neon-serverless';
    import { Pool } from '@neondatabase/serverless';

    const pool = new Pool({ connectionString: process.env.DATABASE_URL! });
    export const db = drizzle(pool);
    ```
  </Tab>
</Tabs>

## Define your schema

Create a `schema.ts` file:

```typescript theme={null}
import { pgTable, serial, text, timestamp } from 'drizzle-orm/pg-core';

export const users = pgTable('users', {
  id: serial('id').primaryKey(),
  name: text('name').notNull(),
  email: text('email').notNull().unique(),
  createdAt: timestamp('created_at').defaultNow(),
});
```

## Query your database

```typescript theme={null}
import { db } from './db';
import { users } from './schema';
import { eq } from 'drizzle-orm';

// Insert a user
await db.insert(users).values({
  name: 'Alice',
  email: 'alice@example.com',
});

// Select all users
const allUsers = await db.select().from(users);

// Select with filter
const user = await db
  .select()
  .from(users)
  .where(eq(users.email, 'alice@example.com'));
```

## Configure Drizzle Kit

Create a `drizzle.config.ts` file for migrations:

```typescript theme={null}
import { defineConfig } from 'drizzle-kit';

export default defineConfig({
  schema: './schema.ts',
  out: './drizzle',
  dialect: 'postgresql',
  dbCredentials: {
    url: process.env.DATABASE_URL!,
  },
});
```

## Run migrations

Generate and apply migrations:

```bash theme={null}
# Generate migration files
npx drizzle-kit generate

# Apply migrations
npx drizzle-kit migrate
```

<Tip>
  For serverless environments, consider using the [Serverless Proxy](/docs/core-concepts/serverless-proxy) to connect over HTTP or WebSocket instead of TCP.
</Tip>
