> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Use Xata with Netlify

> Connect a Netlify site to Xata PostgreSQL, and give every deploy preview its own database branch.

## What is Netlify

[Netlify](https://www.netlify.com) is a platform for building and deploying web applications, with Git-driven builds, serverless functions, and a Deploy Preview for every pull request.

Xata gives a Netlify site a managed PostgreSQL database, and gives each Deploy Preview its own branch of that database with production-like data. Branches are copy-on-write, so a preview branch takes about 3 seconds to create and stores only its diff from the parent. Idle branches scale to zero, so previews that nobody is looking at cost nothing to run.

## Prerequisites

* A Xata account and a project with at least one branch
* A Netlify site
* The [Xata CLI](/docs/cli) installed, if you want to use the CLI commands below or per-preview branches

The CLI commands in this guide read the organization, project, and branch from the folder
they run in. Run [`xata init`](/docs/cli/init) once in your project folder to link it:

```bash theme={null}
xata init
```

Without this, commands such as `xata branch url` fail with
`Missing required path parameter: organizationID`.

## Connect a Netlify site to Xata

1. In the [Xata console](https://console.xata.io), open the branch you want the site to use.
2. On the **Overview** tab, copy the pooled connection string. You can also get it from the CLI:

```bash theme={null}
xata branch url main --type pooler
```

<Tip>
  Use the `pooler` connection type for Netlify Functions. Serverless invocations create many short-lived connections, and the pooler keeps the number of PostgreSQL connections low under concurrency. See [Serverless Proxy](/docs/core-concepts/serverless-proxy) for the full list of connection types.
</Tip>

3. In Netlify, go to **Project configuration > Environment variables** and add a variable named `DATABASE_URL` with the connection string as its value. Leave the scopes at their default so the variable is available to builds and functions on every Netlify plan.
4. Install the driver in your project:

```bash theme={null}
npm install @neondatabase/serverless
```

<Info>
  Xata implements the [Neon serverless driver protocol](https://github.com/neondatabase/serverless), so the `@neondatabase/serverless` package connects to Xata over HTTP and WebSocket without changes. If you are moving a Netlify site off Neon, your data access code stays as it is.
</Info>

5. On the branch's **Queries** page in Xata, create the table used by this example:

```sql theme={null}
CREATE TABLE users (
  id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name TEXT NOT NULL
);

INSERT INTO users (name) VALUES ('Ada Lovelace');
```

6. Query the database from a Netlify Function:

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

7. Deploy the site. The function now reads from your Xata branch.

Standard TCP connections work too, so ORMs and drivers such as Drizzle, Prisma, and `pg` connect with the same connection string. For long-running processes rather than functions, use `xata branch url main` without the `--type pooler` flag.

## Give every Deploy Preview its own database

Netlify builds a Deploy Preview for each pull request. To pair each preview with its own database, create a Xata branch when the pull request opens and set a branch-scoped `DATABASE_URL` on the Netlify site.

This uses a GitHub Actions workflow rather than a Netlify build plugin, because the connection string has to exist before the build starts and has to be readable by the deployed functions at runtime.

### Create the branch when a pull request opens

```yaml theme={null}
name: Create Xata preview branch

on:
  pull_request:
    types: [opened, reopened]

env:
  XATA_API_KEY: ${{ secrets.XATA_API_KEY }}
  XATA_ORGANIZATION_ID: ${{ secrets.XATA_ORGANIZATION_ID }}
  XATA_PROJECT_ID: ${{ secrets.XATA_PROJECT_ID }}
  NETLIFY_AUTH_TOKEN: ${{ secrets.NETLIFY_AUTH_TOKEN }}
  NETLIFY_SITE_ID: ${{ secrets.NETLIFY_SITE_ID }}

jobs:
  create-branch:
    runs-on: ubuntu-latest
    steps:
      - name: Install the Xata CLI
        run: |
          curl -fsSL https://xata.io/install.sh | bash
          echo "$HOME/.config/xata/bin" >> $GITHUB_PATH

      - name: Create a preview branch
        run: |
          xata branch create \
            --organization "$XATA_ORGANIZATION_ID" \
            --project "$XATA_PROJECT_ID" \
            --name "pr-${{ github.event.number }}" \
            --parent-branch "${{ secrets.XATA_PREVIEW_PARENT_BRANCH_ID }}" \
            --scale-to-zero true

      - name: Wait for the branch to be ready
        run: |
          xata branch wait-ready "pr-${{ github.event.number }}" \
            --organization "$XATA_ORGANIZATION_ID" \
            --project "$XATA_PROJECT_ID"

      - name: Point the Deploy Preview at the branch
        run: |
          DATABASE_URL=$(xata branch url "pr-${{ github.event.number }}" --type pooler \
            --organization "$XATA_ORGANIZATION_ID" \
            --project "$XATA_PROJECT_ID")
          if [ -z "$DATABASE_URL" ]; then
            echo "No connection string returned for pr-${{ github.event.number }}" >&2
            exit 1
          fi
          npx netlify-cli env:set DATABASE_URL "$DATABASE_URL" \
            --context branch:${{ github.head_ref }}
```

Netlify applies a branch-scoped value to that branch's Deploy Previews, branch deploys, and deploy permalinks, so the preview build and its functions both pick up the branch connection string. Production keeps whatever `DATABASE_URL` you set on the site.

<Warning>
  The CLI reads `XATA_API_KEY` from the environment, but not the organization or project.
  Outside a folder linked with `xata init`, pass `--organization` and `--project` on every
  command, as the workflow above does. Without them each command fails with
  `Missing required path parameter: projectID`.
</Warning>

### Clean up when the pull request closes

```yaml theme={null}
name: Delete Xata preview branch

on:
  pull_request:
    types: [closed]

env:
  XATA_API_KEY: ${{ secrets.XATA_API_KEY }}
  XATA_ORGANIZATION_ID: ${{ secrets.XATA_ORGANIZATION_ID }}
  XATA_PROJECT_ID: ${{ secrets.XATA_PROJECT_ID }}
  NETLIFY_AUTH_TOKEN: ${{ secrets.NETLIFY_AUTH_TOKEN }}
  NETLIFY_SITE_ID: ${{ secrets.NETLIFY_SITE_ID }}

jobs:
  delete-branch:
    runs-on: ubuntu-latest
    steps:
      - name: Install the Xata CLI
        run: |
          curl -fsSL https://xata.io/install.sh | bash
          echo "$HOME/.config/xata/bin" >> $GITHUB_PATH

      - name: Delete the branch
        run: |
          xata branch delete "pr-${{ github.event.number }}" --yes \
            --organization "$XATA_ORGANIZATION_ID" \
            --project "$XATA_PROJECT_ID"

      - name: Remove the Netlify variable
        run: |
          npx netlify-cli env:unset DATABASE_URL \
            --context branch:${{ github.head_ref }}
```

<Note>
  `XATA_API_KEY` should be an organization API key. Create one with `xata keys organization create`. The other IDs come from `xata organization get id`, `xata project get id`, and `xata branch get id`. Set `XATA_PREVIEW_PARENT_BRANCH_ID` to the ID of the branch that previews should copy. Run these commands from a folder you have linked with `xata init`, on CLI 1.7.0 or later.
</Note>

For the full set of Xata GitHub Actions, including schema migration checks on pull requests, see [Automations](/docs/automations/ga-pr).

## Anonymize preview data

Deploy Previews on a public repository can be triggered by anyone who opens a pull request, so a preview branch forked from production may expose real customer data.

Branch creation copies its parent's data without anonymizing it. Use [`xata clone`](/docs/core-concepts/anonymization) to create an anonymized staging branch, then set `XATA_PREVIEW_PARENT_BRANCH_ID` to that branch's ID. Each preview branch will inherit the sanitized data. See Netlify's [sensitive variable policy](https://docs.netlify.com/build/environment-variables/get-started/#sensitive-variable-policy) for controlling which deploys can read your variables.

## Next steps

* [Branching](/docs/core-concepts/branching): how copy-on-write branches work
* [Scale to Zero](/docs/core-concepts/scale-to-zero): how idle branches hibernate and wake
* [Serverless Proxy](/docs/core-concepts/serverless-proxy): HTTP and WebSocket connections for functions and edge runtimes
