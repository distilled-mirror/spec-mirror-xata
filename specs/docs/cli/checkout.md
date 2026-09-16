> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# checkout

> Check out a branch in this folder

Writes the branch to `.xata/` in this folder, so later commands run against it without being told which branch to use.

This command also takes `-h, --help`.

## checkout

Check out a branch in this folder

Writes the branch to `.xata/` in this folder, so later commands run against it without being told which branch to use.

```bash theme={null}
xata checkout [--organization value] [--project value] [--branch value] [--database value] [--profile value] [--debug] [--json] [<branch>]
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--project" type="string">
  Project ID
</ParamField>

<ParamField path="--branch" type="string">
  Branch ID or name
</ParamField>

<ParamField path="--database" type="string">
  Database name
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean">
  Print where each resolved value came from
</ParamField>

<ParamField path="--json" type="boolean">
  Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.
</ParamField>

<ParamField path="branch" type="string">
  The branch to switch to
</ParamField>

**Examples:**

```bash theme={null}
# Check out a branch of the current project
xata checkout main
# Check out a branch of another project
xata checkout feature-branch --organization org-123 --project proj-456
```
