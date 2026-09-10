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
xata completions install [--profile value] [--debug]
```

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>

## uninstall

Uninstalls bash autocomplete support for xata

```bash theme={null}
xata completions uninstall [--profile value] [--debug]
```

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean" default="false">
  Print where each resolved value came from
</ParamField>
