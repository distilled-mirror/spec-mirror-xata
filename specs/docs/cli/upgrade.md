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

**--channel** `dev | next | latest` (default: latest)

The channel to upgrade from

**--version** `string`

The version to upgrade to

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**Examples:**

```bash theme={null}
# Move to a specific version
xata upgrade --version 1.5.4
# Follow the pre-release channel
xata upgrade --channel next
```


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
