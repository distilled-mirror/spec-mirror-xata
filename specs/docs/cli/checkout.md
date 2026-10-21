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

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID or name

**--database** `string`

Database name

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**branch** `string`

The branch to switch to

**Examples:**

```bash theme={null}
# Check out a branch of the current project
xata checkout main
# Check out a branch of another project
xata checkout feature-branch --organization org-123 --project proj-456
```


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
