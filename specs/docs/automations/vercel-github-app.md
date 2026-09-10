> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Vercel preview deployments

> Connect Vercel preview deployments to Xata branches created by the GitHub App

Vercel creates a preview deployment for each Git branch. The Xata GitHub App creates a Xata branch for each pull request and gives it the same name as the pull request's source branch.

You can use that shared branch name to connect every Vercel preview deployment to its matching Xata branch. Vercel exposes the Git branch as `VERCEL_GIT_COMMIT_REF`, and a custom build script passes it to `xata branch url` to retrieve the connection string.

This guide uses Next.js as an example. If you use another framework or package manager, adapt the environment file and build commands to your application.

## How it works

1. You push a Git branch and open a pull request.
2. The Xata GitHub App creates a Xata branch named after the pull request's source branch.
3. Subsequent pushes reuse that Xata branch. The GitHub App does not recreate or refresh it on every push.
4. During each preview build, Vercel sets `VERCEL_GIT_COMMIT_REF` to that same branch name.
5. The build script retrieves the Xata branch's connection string and makes it available to the application as `DATABASE_URL`.
6. When the pull request closes, the Xata GitHub App deletes the Xata branch.

## Prerequisites

* [Install the Xata GitHub App and link the repository to a Xata project](/docs/automations/github-app).
* Connect the same GitHub repository to a Vercel project.
* Configure your application to read its PostgreSQL connection string from `DATABASE_URL`.

<Note>
  This guide uses the Xata GitHub App to manage the branch lifecycle. You can use the [GitHub Actions pull request workflow](/docs/automations/ga-pr) instead, but its example names branches `<source-branch>_<pull-request-number>`. Adjust the branch name in the Vercel script to match your workflow.
</Note>

## Create an API key for Vercel

The Vercel build uses the Xata CLI to find the preview branch and retrieve its connection string. Create a dedicated API key for this automation:

1. Open [User API keys](https://console.xata.io/account/api-keys) in the Xata Console.
2. Click **New API Key**.
3. Give the key a descriptive name, such as `Vercel preview deployments`, and choose an appropriate expiration.
4. Under **Projects & Branches**, select the Xata project linked to the repository. Select the project rather than an individual branch so the key can access preview branches created later.
5. Enable **Access control** and select the **CI/CD deploy bot** preset.
6. Create the key and copy it. The full value is shown only once.

<img src="https://mintcdn.com/xata/39VoIPEG0avOsZrO/images/automations/vercel-api-key.png?fit=max&auto=format&n=39VoIPEG0avOsZrO&q=85&s=e3a76b822f2cf055c092d030edfe693c" alt="Xata user API key form scoped to one project with the CI/CD deploy bot preset selected" className="rounded-lg" width="2456" height="1732" data-path="images/automations/vercel-api-key.png" />

See [API keys](/docs/platform/api-key) for more information about project restrictions, scopes, and expiration.

<Warning>
  Treat the API key like a password. Do not commit it to the repository or print it in build logs.
</Warning>

## Configure Vercel environment variables

In the Vercel project, open **Settings** > **Environment Variables** and add these variables for the **Preview** environment:

| Variable               | Value                                                                                      |
| ---------------------- | ------------------------------------------------------------------------------------------ |
| `XATA_API_KEY`         | The API key created in the previous section. Mark it as **Sensitive**.                     |
| `XATA_ORGANIZATION_ID` | The organization ID from the Xata Console project URL, or from `xata organization get id`. |
| `XATA_PROJECT_ID`      | The project ID from the Xata Console project URL, or from `xata project get id`.           |

The Xata CLI automatically uses these standard environment variables for authentication and project selection. You do not need to install the CLI locally if you copy the organization and project IDs from a Console URL such as `https://console.xata.io/organizations/<organization-id>/projects/<project-id>`.

<Note>
  Add `XATA_API_KEY` to Vercel because the connection-string lookup runs in the Vercel build. If you use GitHub Actions to manage the branch lifecycle, also add the key as a [GitHub Actions secret](https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/use-secrets) named `XATA_API_KEY` for that workflow.
</Note>

For production, configure `DATABASE_URL` separately in Vercel's **Production** environment. For example, get the primary connection string for the Xata production branch with:

```bash theme={null}
xata branch url main --type primary
```

The build script below only derives `DATABASE_URL` dynamically for preview deployments. Production deployments continue to use the value configured in Vercel.

### Enable Vercel system environment variables

The script requires `VERCEL_ENV` and `VERCEL_GIT_COMMIT_REF`. Vercel provides both as [system environment variables](https://vercel.com/docs/environment-variables/system-environment-variables).

In the Vercel project's **Environment Variables** settings, select **Enable access to System Environment Variables**. `VERCEL_GIT_COMMIT_REF` contains the Git branch that triggered the deployment.

## Add the build script

Create `scripts/vercel-build.sh` in your application repository:

```bash theme={null}
#!/usr/bin/env bash
# Vercel build entry point. For preview deploys, use the Xata branch created
# by the GitHub App and point DATABASE_URL at it. Production builds use the
# DATABASE_URL configured in the Vercel project.

# Do not enable `set -x`: it can print the connection string to build logs.
set -euo pipefail

if [[ "${VERCEL_ENV:-}" == "preview" && -n "${VERCEL_GIT_COMMIT_REF:-}" ]]; then
  # Install the CLI if the binary is not found.
  if ! command -v xata >/dev/null 2>&1; then
    curl -fsSL https://xata.io/install.sh | bash
    export PATH="$HOME/.config/xata/bin:$PATH"
  fi

  # Sanity check that the CLI was installed successfully.
  xata version

  # With the GitHub App, the Xata branch name matches the Git branch name.
  # If GitHub Actions uses another naming pattern, change this assignment.
  BRANCH_NAME="${VERCEL_GIT_COMMIT_REF}"

  # The GitHub App creates the branch once when the PR opens. Preview builds
  # reuse it instead of deleting and recreating it on every push.
  xata branch wait-ready "$BRANCH_NAME"

  DSN=$(xata branch url "$BRANCH_NAME" --type primary)
  echo "DATABASE_URL=$DSN" >> .env.production

  DATABASE_URL="$DSN" npm run db:migrate
fi

npm run build
```

Shell tracing (`set -x`) is intentionally disabled so the connection string is not written to Vercel build logs.

The `db:migrate` command applies the application's migrations to the preview branch. Remove that line if your application does not have a `db:migrate` script.

The GitHub App does not recreate the Xata branch on every push. Keeping the branch for the lifetime of the pull request preserves its branch ID and connection URL. Xata does not currently provide a branch-reset operation; deleting and recreating the branch would give it a new ID and URL.

The lookup fails if the matching Xata branch cannot be found. Do not add a fallback to the production connection string: a failed preview build is safer than a preview deployment that writes to production.

<Note>
  The `.env.production` line is specific to Next.js. If you use another framework, use its supported build-time mechanism to include `DATABASE_URL` in server-side deployment output. Never expose the connection string through a public or client-prefixed environment variable.
</Note>

Add a script entry to `package.json` while keeping the existing `build` script unchanged:

```json theme={null}
{
  "scripts": {
    "build": "next build",
    "vercel-build": "bash scripts/vercel-build.sh"
  }
}
```

## Override the Vercel build command

In Vercel:

1. Open the project and go to **Settings** > [**Build and Deployment**](https://vercel.com/docs/builds/configure-a-build#build-command).
2. Under **Framework Settings**, enable **Override** for **Build Command**.
3. Set the command to `pnpm vercel-build`.
4. Save the settings and redeploy.

If your project uses npm, use `npm run vercel-build` instead.

<Note>
  Vercel may detect a `vercel-build` script automatically for some frameworks, but this behavior is not documented and may change. Set the Build Command explicitly in the Vercel project settings.
</Note>

<img src="https://mintcdn.com/xata/39VoIPEG0avOsZrO/images/automations/vercel-build-command.png?fit=max&auto=format&n=39VoIPEG0avOsZrO&q=85&s=ebe25a3b6d2f8169f0729f016d72a90f" alt="Vercel Framework Settings with the Build Command override set to pnpm vercel-build" className="rounded-lg" width="2568" height="1204" data-path="images/automations/vercel-build-command.png" />

## Verify the integration

1. Open a pull request in the linked GitHub repository.
2. Wait for the Xata GitHub App's pull request comment confirming that the preview branch was created.
3. Redeploy the Vercel preview if its first build started before the Xata branch was created.
4. Check the Vercel build log for the Xata branch name and a successful application build. The connection string itself should not appear in the log.
5. Open the preview deployment and verify that it reads from or writes to the preview branch, not the production branch.

## Troubleshooting

### `VERCEL_GIT_COMMIT_REF` is not set

Select **Enable access to System Environment Variables** in the Vercel project's environment variable settings, then redeploy.

### The Xata branch cannot be found

The GitHub App creates a branch when a pull request is opened or reopened, while Vercel can build a Git branch before a pull request exists. Confirm that the GitHub App posted a successful branch-creation comment, then redeploy the Vercel preview.

Also check that the same repository is linked to both projects and that your branch-lifecycle automation uses the expected naming convention.

### The Xata CLI cannot authenticate or select a project

Confirm that `XATA_API_KEY`, `XATA_ORGANIZATION_ID`, and `XATA_PROJECT_ID` are set for Vercel's **Preview** environment. Make sure the API key is scoped to the linked Xata project, not only to its root branch.

### The preview still connects to the production database

Confirm that your server-side application reads `DATABASE_URL` and that your framework includes the dynamically generated value in its deployment output. For Next.js, keep the `.env.production` step before `next build`.

### A preview stops working after the pull request closes

This is expected. Closing the pull request causes the Xata GitHub App to delete its preview branch, so existing Vercel deployments for that pull request can no longer connect to it.

### A pull request comes from a fork

Vercel protects environment variables for deployments from forks. An authorized project member must approve the deployment before it can access the Xata API key. Do not disable this protection for a project that contains secrets.
