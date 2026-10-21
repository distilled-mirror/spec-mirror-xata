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
xata init [--organization value] [--project value] [--branch value] [--database value] [--profile value] [--debug] [--json]
```

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--database** `string`

Database name on the branch

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**Examples:**

```bash theme={null}
# Link without prompts
xata init --organization <org-id> --project <project-id> --branch <branch-id>
```


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
