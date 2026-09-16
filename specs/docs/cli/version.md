> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# version

> Get the version of the Xata CLI, pgroll and pgstream

This command also takes `-h, --help`.

## version

Get the version of the Xata CLI, pgroll and pgstream

```bash theme={null}
xata version [--skip-download] [--profile value] [--debug] [--json]
```

<ParamField path="--skip-download" type="boolean" default="false">
  Skip downloading the pgroll/pgstream binaries
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
