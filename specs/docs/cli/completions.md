> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# completions

> Install or uninstall shell completions for the Xata CLI

Only bash is supported, and the completions are picked up the next time the shell starts.

Every command below also takes `-h, --help`.

* [`xata completions install`](#install) — Installs bash autocomplete support for xata
* [`xata completions uninstall`](#uninstall) — Uninstalls bash autocomplete support for xata

## install

Installs bash autocomplete support for xata

```bash theme={null}
xata completions install [--profile value] [--debug] [--json]
```

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

## uninstall

Uninstalls bash autocomplete support for xata

```bash theme={null}
xata completions uninstall [--profile value] [--debug] [--json]
```

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
