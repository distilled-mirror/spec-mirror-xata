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

**--organization** `string`

Organization ID

**--project** `string`

Project ID

**--branch** `string`

Branch ID

**--database** `string`

Database name

**--type** `primary | primary-or-replica | replica | pooler` (default: primary)

Connection type: primary (direct access to the primary), primary-or-replica (routed access to primary or replicas), replica (read-only access to replicas only), pooler (pooled access to the primary)

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
