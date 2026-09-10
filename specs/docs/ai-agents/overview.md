> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Xata for Agents

> Set up Xata for AI coding agents. Install the CLI, configure MCP, and use agent skills.

# Postgres for AI coding agents

Xata exposes a CLI, a hosted MCP server, and agent skills. AI coding agents can use any of them to create Postgres branches, run migrations, inspect schema, and work against realistic data without writing to production.

## Get set up

New to Xata? Hand the setup to your agent: the [agent onboarding guide](/docs/ai-agents/onboarding) walks it through installing the CLI, creating a project, connecting your app, and trying branching. The [Quickstart](/docs/quickstart) has a ready-made prompt.

### Or set up manually

If you do not have a Xata account yet, [sign up and create a project](/docs/quickstart) first.

Install the Xata CLI, authenticate, and link your local repository:

```bash theme={null}
curl -fsSL https://xata.io/install.sh | bash
xata auth login
xata init
```

`xata init` writes your organization, project, branch, and database to `.xata/` in the current folder so later CLI commands know which project to use.

Connect the Xata MCP server in your agent client. Most clients only need the server URL; OAuth handles the rest:

```
https://api.xata.tech/mcp
```

Example for Claude Code:

```bash theme={null}
claude mcp add --transport http xata https://api.xata.tech/mcp
```

See [MCP Server](/docs/platform/mcp) for setup instructions for Cursor, VS Code, Codex, OpenCode, Windsurf, Cline, and other clients.

## Choose your integration

<CardGroup cols={2}>
  <Card title="Xata CLI" icon="terminal" href="/docs/cli">
    Branches, auth, migrations, clone, stream, and project linking from the terminal.
  </Card>

  <Card title="Xata MCP" icon="plug" href="/docs/platform/mcp">
    Connect through OAuth or an API key. Works with Cursor, Claude Code, VS Code, Codex, OpenCode, Windsurf, Cline, and more.
  </Card>

  <Card title="Agent Skills" icon="star" href="/docs/ai-agents/agent-skills">
    Guided workflows for the CLI, HTTP API, and PostgreSQL operations. Available through the MCP server.
  </Card>

  <Card title="Agent guides" icon="robot" href="/docs/ai-agents/claude-code">
    Step-by-step setup for Claude Code, Cursor, Codex, Copilot, and other coding agents with isolated branches.
  </Card>
</CardGroup>

## When to use each

* **Xata MCP**: preferred for agent-native operations. Connect to `https://api.xata.tech/mcp` with OAuth or an API key. Exposes REST API operations, schema inspection, SQL, documentation search, and skills without shelling out to the CLI for every step.
* **Xata CLI**: preferred when the task depends on local machine state: linking the current directory with `xata init`, creating branches, running migrations with `xata roll`, cloning data with `xata clone`, and scripting in CI.
* **Agent Skills**: use alongside MCP so agents follow Xata-specific procedures for CLI commands, API calls, and PostgreSQL troubleshooting instead of guessing.
* **Agent guides**: use when you want a concrete workflow for a specific tool: branch connection strings, `.env.local` setup, example prompts, and cleanup steps for that agent.

## Working with sensitive data

<CardGroup cols={1}>
  <Card title="Anonymized production clones" icon="user-shield" href="/docs/core-concepts/anonymization">
    Give agents realistic schema and data for testing without exposing PII or other sensitive values. Create an anonymized clone as the parent branch before spinning up agent task branches.
  </Card>
</CardGroup>

## Agent directory

Use the guide for your coding agent to create an isolated branch, pass the branch connection string to the agent, and keep schema or data changes scoped to that branch until you review them.

<CardGroup cols={2}>
  <Card title="Claude Code" icon="terminal" href="/docs/ai-agents/claude-code">
    Terminal-first workflows for branch-scoped migrations, tests, and database inspection.
  </Card>

  <Card title="Codex" icon="code" href="/docs/ai-agents/codex">
    Sandbox-friendly workflows for running tests and edits against a dedicated Xata branch.
  </Card>

  <Card title="Cursor" icon="arrow-pointer" href="/docs/ai-agents/cursor">
    IDE and background-agent setup with `.env.local` connection strings and reviewable schema changes.
  </Card>

  <Card title="OpenCode" icon="terminal" href="/docs/ai-agents/opencode">
    Provider-agnostic terminal agent workflows using standard Postgres and the Xata CLI.
  </Card>

  <Card title="GitHub Copilot" icon="github" href="/docs/ai-agents/github-copilot">
    VS Code and GitHub workflows for per-PR branches and CI-backed database checks.
  </Card>

  <Card title="Windsurf" icon="wind" href="/docs/ai-agents/windsurf">
    Cascade and Flow patterns for repeatable branch creation, migration testing, and cleanup.
  </Card>

  <Card title="Cline" icon="check-double" href="/docs/ai-agents/cline">
    Approval-based VS Code workflows with safe terminal commands and branch-scoped credentials.
  </Card>

  <Card title="Devin" icon="cloud" href="/docs/ai-agents/devin">
    Long-running cloud-agent tasks with isolated branches, scoped credentials, and explicit cleanup.
  </Card>
</CardGroup>

## Recommended baseline

1. Create or choose a parent branch with the schema and data the agent should use. For sensitive data, start from an [anonymized production clone](/docs/core-concepts/anonymization).
2. Create a dedicated branch for the agent task.
3. Give the agent only the branch connection string or a [branch-scoped API key](/docs/platform/api-key).
4. Ask the agent to run migrations, tests, and data changes only against that branch.
5. Review code, SQL, schema diffs, and Xata branch state before merging.
6. Delete the temporary branch when the task is done.

```bash theme={null}
xata branch create --name agent-task --parent-branch `xata branch get id`
xata branch url agent-task
```

## See also

* [Data Anonymization](/docs/core-concepts/anonymization)
* [API Keys](/docs/platform/api-key)
* [Create a branch per pull request](/docs/automations/ga-pr)
