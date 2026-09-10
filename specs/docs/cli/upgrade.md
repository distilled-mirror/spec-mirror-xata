> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# upgrade

> Upgrade the Xata CLI

This command also takes `-h, --help`.

## upgrade

Upgrade the Xata CLI

```bash theme={null}
xata upgrade [--channel dev|next|latest] [--version value] [--profile value] [--debug]
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

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

**Examples:**

```bash theme={null}
# Move to a specific version
xata upgrade --version 1.5.4
# Follow the pre-release channel
xata upgrade --channel next
```
