> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# upgrade

> Upgrade the Xata CLI

This command also takes `-h, --help`.

## upgrade

Upgrade the Xata CLI

```bash theme={null}
xata upgrade [--channel dev|next|latest] [--version value] [--profile value] [--debug] [--json]
```

<ParamField path="--channel" type="dev | next | latest" default="latest">
  The channel to upgrade from
</ParamField>

<ParamField path="--version" type="string">
  The version to upgrade to
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

**Examples:**

```bash theme={null}
# Move to a specific version
xata upgrade --version 1.5.4
# Follow the pre-release channel
xata upgrade --channel next
```
