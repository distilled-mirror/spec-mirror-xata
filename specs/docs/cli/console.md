> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# console

> Open an interactive branch console

This command also takes `-h, --help`.

## console

Open an interactive branch console

```bash theme={null}
xata console [--organization value] [--project value] [--branch value] [--database value] [--type primary|primary-or-replica|replica|pooler] [--profile value] [--debug] [--json]
```

<ParamField path="--organization" type="string">
  Organization ID
</ParamField>

<ParamField path="--project" type="string">
  Project ID
</ParamField>

<ParamField path="--branch" type="string">
  Branch ID
</ParamField>

<ParamField path="--database" type="string">
  Database name
</ParamField>

<ParamField path="--type" type="primary | primary-or-replica | replica | pooler" default="primary">
  Connection type: primary (direct access to the primary), primary-or-replica (routed access to primary or replicas), replica (read-only access to replicas only), pooler (pooled access to the primary)
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
