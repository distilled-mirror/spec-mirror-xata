> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# status

> Show the organization, project, and branch this folder uses

Reads the context from the `XATA_*` variables and the local config, so it is the quickest way to see which branch the commands run here will act on.

This command also takes `-h, --help`.

## status

Show the organization, project, and branch this folder uses

Reads the context from the `XATA_*` variables and the local config, so it is the quickest way to see which branch the commands run here will act on.

```bash theme={null}
xata status [--json] [--profile value] [--debug]
```

<ParamField path="--json" type="boolean" default="false">
  Output in JSON format
</ParamField>

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>
