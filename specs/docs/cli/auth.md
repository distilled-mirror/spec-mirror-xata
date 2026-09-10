> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# auth

> Authenticate with Xata

Sessions are stored as profiles, so several accounts and environments can be used side by side. Commands run against the active profile unless `--profile` names another one.

Every command below also takes `-h, --help`.

* [`xata auth login`](#login) — Log in to a Xata account
* [`xata auth logout`](#logout) — Log out of the current account
* [`xata auth status`](#status) — Display active account and authentication state
* [`xata auth switch`](#switch) — Switch to a different Xata account profile
* [`xata auth list`](#list) — List all available Xata account profiles
* [`xata auth refresh`](#refresh) — Refresh the access token of the current session
* [`xata auth access-token`](#access-token) — Print the current access token
* [`xata auth refresh-token`](#refresh-token) — Print the stored refresh token, without refreshing the session

## login

Log in to a Xata account

Prints a URL and a code to authorize this machine, or stores an API key with `--api-key` for non-interactive use. The issuer, API URL and client flags log in against a deployment other than production, which is how Enterprise customers connect the CLI to a custom deployment in their own cloud. Omit them and the CLI uses the default production values.

```bash theme={null}
xata auth login [--profile value] [--force] [--api-key value] [--issuer value] [--api-url value] [--client-id value] [--client-secret value] [--debug]
```

<ParamField path="--profile" type="string" default="default">
  The profile to log in to
</ParamField>

<ParamField path="-f, --force" type="boolean" default="false">
  Force login even if already logged in
</ParamField>

<ParamField path="--api-key" type="string">
  Log in non-interactively with an API key instead of the browser OAuth flow
</ParamField>

<ParamField path="--issuer" type="string">
  Issuer URL for custom environment
</ParamField>

<ParamField path="--api-url" type="string">
  API base URL for custom environment
</ParamField>

<ParamField path="--client-id" type="string">
  Client ID for custom environment (defaults to "cli")
</ParamField>

<ParamField path="--client-secret" type="string">
  Client secret for custom environment
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

**Examples:**

```bash theme={null}
# Log in from a script or CI
xata auth login --api-key xau_...
# Log in as another profile
xata auth login --profile staging
# Log in to another deployment
xata auth login --profile staging --api-url https://api.staging.example.com
```

## logout

Log out of the current account

```bash theme={null}
xata auth logout [--profile value] [--yes] [--debug]
```

<ParamField path="--profile" type="string" default="default">
  The profile to log out of
</ParamField>

<ParamField path="--yes" type="boolean" default="false">
  Do not ask for confirmation, assume yes.
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

## status

Display active account and authentication state

Reads the stored session without contacting the server, so it stays fast and works offline. It reports an expiry the stored session has already passed, but it cannot see a session revoked server-side. Use `xata auth refresh` to verify against the server.

```bash theme={null}
xata auth status [--profile value] [--debug]
```

<ParamField path="--profile" type="string">
  The profile to check
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

## switch

Switch to a different Xata account profile

```bash theme={null}
xata auth switch [--profile value] [--debug] [<arg1>]
```

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

<ParamField path="value" type="string">
  The profile to switch to
</ParamField>

## list

List all available Xata account profiles

```bash theme={null}
xata auth list [--json] [--profile value] [--debug]
```

<ParamField path="--json" type="boolean">
  Output in JSON format
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

**Aliases:** `xata auth ls`

## refresh

Refresh the access token of the current session

Refreshes the session even when the current access token is still valid, and stores the new one. Every command already refreshes on its own when the token is about to expire, so this is for scripts that want to fail early, or to rotate the token before a long job.

```bash theme={null}
xata auth refresh [--profile value] [--json] [--debug]
```

<ParamField path="--profile" type="string">
  The profile to refresh
</ParamField>

<ParamField path="--json" type="boolean" default="false">
  Output in JSON format
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

## access-token

Print the current access token

```bash theme={null}
xata auth access-token [--profile value] [--debug]
```

<ParamField path="--profile" type="string" default="default">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

## refresh-token

Print the stored refresh token, without refreshing the session

```bash theme={null}
xata auth refresh-token [--profile value] [--debug]
```

<ParamField path="--profile" type="string" default="default">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>
