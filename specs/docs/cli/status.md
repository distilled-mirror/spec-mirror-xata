> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# status

> Show the organization, project, branch, and database this folder uses

Reads the context from the `XATA_*` variables and the local config, so it is the quickest way to see which branch and database the commands run here will act on.

This command also takes `-h, --help`.

## status

Show the organization, project, branch, and database this folder uses

Reads the context from the `XATA_*` variables and the local config, so it is the quickest way to see which branch and database the commands run here will act on.

```bash theme={null}
xata status [--profile value] [--debug] [--json]
```

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
