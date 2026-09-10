> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Agent Skills

> Guided workflows that teach AI coding agents how to operate Xata.

# Agent Skills

Agent skills extend AI coding assistants with Xata-specific knowledge: branch workflows, CLI commands, API conventions, and PostgreSQL troubleshooting.

## What are agent skills?

Agent skills follow the [Agent Skills](https://agentskills.io) open format. Each skill is a markdown file (`SKILL.md`) with metadata, step-by-step instructions, and reference files for common tasks.

When you ask an agent to create a branch, run a migration, or diagnose a slow query, it can load the matching skill and follow Xata-specific procedures instead of guessing.

### Supported tools

Any MCP client connected to the [Xata MCP server](/docs/platform/mcp) can list and read skills through the `list_skills` and `get_skill` tools, including Claude Code, Cursor, Codex, and any other client documented on the MCP setup page.

## Available skills

| Skill                 | Use when                                                                                                                                     |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `using-xata-cli`      | Working with projects, branches, auth, migrations, clone, keys, or organizations from the terminal                                           |
| `using-xata-api`      | Calling the Xata HTTP API directly, scripting against `api.xata.tech`, or building apps that manage Xata programmatically                    |
| `managing-postgresql` | Diagnosing slow queries, high CPU, connection issues, locks, vacuum, indexes, backups, replication, or other PostgreSQL problems on a branch |

## Access skills through MCP

After connecting to the Xata MCP server, ask your agent:

> List the available Xata skills.

The agent calls `list_skills` and returns the skill catalog. To load a skill:

> Read the `using-xata-cli` skill and create a development branch for this task.

The agent calls `get_skill` with the skill name and follows the instructions.

<Tip>
  Skills are served by the hosted MCP server. There is nothing to install locally. Connect once in your agent client and the skills are available automatically.
</Tip>

## Related docs

* [Xata for Agents](/docs/ai-agents/overview)
* [MCP Server](/docs/platform/mcp)
* [CLI Reference](/docs/cli)
