> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# skill

> Discover and install Xata agent skills

Every command below also takes `-h, --help`.

* [`xata skill list`](#list) — List available skills from xataio/skills
* [`xata skill install`](#install) — Install a skill; select interactively or list when no name is given

## list

List available skills from xataio/skills

```bash theme={null}
xata skill list [--profile value] [--debug] [--json]
```

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

## install

Install a skill; select interactively or list when no name is given

```bash theme={null}
xata skill install [--agent claude-code|codex|cursor|amp|github-copilot|antigravity-cli|opencode|windsurf|zed|cline] [--global] [--directory value] [--force] [--profile value] [--debug] [--json] [<name>]
```

**--agent** `claude-code | codex | cursor | amp | github-copilot | antigravity-cli | opencode | windsurf | zed | cline`

Target agent; repeat to select multiple agents

**--global** `boolean`

Install at user scope instead of project scope

**--directory** `string`

Project root (defaults to the current directory); not the final skill directory

**--force** `boolean`

Replace differing existing skills, discarding local edits

**--profile** `string`

The profile to use

**--debug** `boolean`

Print where each resolved value came from

**--json** `boolean`

Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.

**name** `string`

Skill name from xataio/skills


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
