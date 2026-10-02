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

<ParamField path="--profile" type="string">
  The profile to use
</ParamField>

<ParamField path="--debug" type="boolean">
  Print where each resolved value came from
</ParamField>

<ParamField path="--json" type="boolean">
  Output in JSON format when the command supports it. Defaults to on when an AI agent runs the command.
</ParamField>

## install

Install a skill; select interactively or list when no name is given

```bash theme={null}
xata skill install [--agent claude-code|codex|cursor|amp|github-copilot|antigravity-cli|opencode|windsurf|zed|cline] [--global] [--directory value] [--force] [--profile value] [--debug] [--json] [<name>]
```

<ParamField path="--agent" type="claude-code | codex | cursor | amp | github-copilot | antigravity-cli | opencode | windsurf | zed | cline">
  Target agent; repeat to select multiple agents
</ParamField>

<ParamField path="--global" type="boolean">
  Install at user scope instead of project scope
</ParamField>

<ParamField path="--directory" type="string">
  Project root (defaults to the current directory); not the final skill directory
</ParamField>

<ParamField path="--force" type="boolean">
  Replace differing existing skills, discarding local edits
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

<ParamField path="name" type="string">
  Skill name from xataio/skills
</ParamField>


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
