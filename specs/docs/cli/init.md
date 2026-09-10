> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# init

> Link this folder to a project and branch

Writes the organization, project, branch and database to `.xata/` in this folder, so the commands run here no longer need them passed in.

This command also takes `-h, --help`.

## init

Link this folder to a project and branch

Writes the organization, project, branch and database to `.xata/` in this folder, so the commands run here no longer need them passed in.

```bash theme={null}
xata init [--organization value] [--project value] [--branch value] [--database value] [--json] [--profile value] [--debug]
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
  Database name on the branch
</ParamField>

<ParamField path="--json" type="boolean" default="false">
  Output in JSON format
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

**Examples:**

```bash theme={null}
# Link without prompts
xata init --organization <org-id> --project <project-id> --branch <branch-id>
```
