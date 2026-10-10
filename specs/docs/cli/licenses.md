> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# licenses

> Show third-party software notices and licenses bundled with the Xata CLI

Prints the NOTICE bundled with the CLI, which identifies the statically linked LGPL components (JavaScriptCore/WebKit, tinycc) embedded via the Bun runtime and how to obtain their corresponding source and relink. Use --full to also print the full license texts.

This command also takes `-h, --help`.

## licenses

Show third-party software notices and licenses bundled with the Xata CLI

Prints the NOTICE bundled with the CLI, which identifies the statically linked LGPL components (JavaScriptCore/WebKit, tinycc) embedded via the Bun runtime and how to obtain their corresponding source and relink. Use --full to also print the full license texts.

```bash theme={null}
xata licenses [--full] [--profile value] [--debug] [--json]
```

<ParamField path="--full" type="boolean" default="false">
  Also print the full texts of the GNU LGPL-2.0, LGPL-2.1 and GPL-2.0 licenses
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


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
