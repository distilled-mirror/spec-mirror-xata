> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# MCP Server

> Connect Cursor, Claude, VS Code, and other MCP clients to Xata

The Xata MCP server lets AI assistants and agents interact with your Xata organizations, projects, and branches using the [Model Context Protocol](https://modelcontextprotocol.io) (MCP).

## What is the Xata MCP server?

* A hosted MCP server that runs alongside the Xata API — there is nothing to install or run locally.
* Authenticated via OAuth in your browser, or with a Xata API key for headless environments.
* Accessible from any MCP client that supports remote servers over Streamable HTTP.

**Server URL:**

```
https://api.xata.tech/mcp
```

The server uses the **Streamable HTTP** transport. There is no SSE endpoint and no local (npm) version of the server.

## Authentication

The MCP server supports two authentication methods:

| Method  | Use when                          | Client requirement                                  |
| ------- | --------------------------------- | --------------------------------------------------- |
| OAuth   | Interactive use in an editor/chat | Support for MCP OAuth (dynamic client registration) |
| API key | Automation, CI, headless agents   | Support for custom HTTP headers                     |

### OAuth

With OAuth-capable clients, you only need the server URL. When your client connects for the first time, it registers itself with Xata, opens a browser window, and asks you to sign in to your Xata account and approve access. Tokens are short-lived and scoped to the MCP server.

### API key

Clients that support custom headers can authenticate with a [Xata API key](/docs/platform/api-key) instead:

```
Authorization: Bearer YOUR_XATA_API_KEY
```

<Warning>
  Create a dedicated API key for MCP access rather than reusing an existing key. Store it in an environment variable or your client's secret storage — never commit it to source control.
</Warning>

## Set up your MCP client

### Cursor

<Tip title="CURSOR USERS: ONE-CLICK ALTERNATIVE">
  Cursor offers a deep link for quick OAuth setup:

  <a href="cursor://anysphere.cursor-deeplink/mcp/install?name=xata&config=eyJ1cmwiOiJodHRwczovL2FwaS54YXRhLnRlY2gvbWNwIn0%3D" style={{ display: 'inline-flex', alignItems: 'center', gap: '8px', padding: '8px 12px', backgroundColor: '#111111', color: '#ffffff', borderRadius: '6px', fontWeight: '500', textDecoration: 'none', marginTop: '8px', marginBottom: '16px' }}>
    <svg width="14" height="16" viewBox="0 0 466.73 532.09" xmlns="http://www.w3.org/2000/svg">
      <path fill="#ffffff" d="M457.43,125.94L244.42,2.96c-6.84-3.95-15.28-3.95-22.12,0L9.3,125.94c-5.75,3.32-9.3,9.46-9.3,16.11v247.99c0,6.65,3.55,12.79,9.3,16.11l213.01,122.98c6.84,3.95,15.28,3.95,22.12,0l213.01-122.98c5.75-3.32,9.3-9.46,9.3-16.11v-247.99c0-6.65-3.55-12.79-9.3-16.11h-.01ZM444.05,151.99l-205.63,356.16c-1.39,2.4-5.06,1.42-5.06-1.36v-233.21c0-4.66-2.49-8.97-6.53-11.31L24.87,145.67c-2.4-1.39-1.42-5.06,1.36-5.06h411.26c5.84,0,9.49,6.33,6.57,11.39h-.01Z" />
    </svg>

    <span style={{ color: '#ffffff' }}>Add to Cursor</span>
  </a>
</Tip>

Alternatively, you can add it manually:

1. Open the command palette and search for "Cursor Settings".
2. Under **Tools & MCP**, click **New MCP Server**.
3. Add the Xata server to the configuration file that opens:

```json .cursor/mcp.json theme={null}
{
  "mcpServers": {
    "xata": {
      "url": "https://api.xata.tech/mcp"
    }
  }
}
```

4. Save the file. Cursor prompts you to authenticate — follow the browser flow and approve access to your Xata account.

### Claude Code

Add the server from your terminal:

```bash theme={null}
claude mcp add --transport http xata https://api.xata.tech/mcp
```

Then start Claude Code and run the `/mcp` slash command. Select the `xata` server and follow the browser instructions to authenticate.

To use an API key instead of OAuth (for example, in CI):

```bash theme={null}
claude mcp add --transport http xata https://api.xata.tech/mcp \
  --header "Authorization: Bearer YOUR_XATA_API_KEY"
```

### VS Code

MCP servers in VS Code require the [GitHub Copilot](https://marketplace.visualstudio.com/items?itemName=github.copilot) and [GitHub Copilot Chat](https://marketplace.visualstudio.com/items?itemName=github.copilot-chat) extensions.

1. Open the Command Palette (`Cmd+Shift+P` / `Ctrl+Shift+P`).
2. Run **MCP: Add Server** and choose **HTTP**.
3. Enter `https://api.xata.tech/mcp` as the URL and `xata` as the name.

Alternatively, add it to your configuration manually:

```json .vscode/mcp.json theme={null}
{
  "servers": {
    "xata": {
      "type": "http",
      "url": "https://api.xata.tech/mcp"
    }
  }
}
```

Start the server from **MCP: List Servers** and allow it to authenticate when prompted.

### Claude (web and desktop)

<Tip title="CLAUDE USERS: QUICK SETUP">
  Open Claude's custom connector dialog with Xata's details prefilled:

  <a href="https://claude.ai/customize/connectors?modal=add-custom-connector&connectorName=Xata&connectorUrl=https%3A%2F%2Fapi.xata.tech%2Fmcp" style={{ display: 'inline-flex', alignItems: 'center', padding: '8px 12px', backgroundColor: '#735adc', color: '#ffffff', borderRadius: '6px', fontWeight: '500', textDecoration: 'none', marginTop: '8px', marginBottom: '16px' }}>
    <span style={{ color: '#ffffff' }}>Connect Xata to Claude</span>
  </a>

  Review and confirm the connector in Claude, then authenticate with Xata.
</Tip>

Alternatively, add Xata as a custom connector manually:

1. Go to **Settings** → **Connectors**.
2. Click **Add custom connector**.
3. Enter `https://api.xata.tech/mcp` as the server URL and click **Add**.
4. Follow the prompts to sign in with your Xata account.

<Note>
  Custom connectors using remote MCP are not available on all Claude plans, and may require an organization owner to add them on team plans. See the [Claude documentation](https://support.claude.com/en/articles/11175166-getting-started-with-custom-connectors-using-remote-mcp) for details.
</Note>

### ChatGPT

Connect ChatGPT to Xata using a custom connector:

1. In ChatGPT, go to **Settings** → **Connectors** → **Advanced settings** and enable **Developer mode**.
2. On the Connectors tab, create a new connector with the server URL:

```
https://api.xata.tech/mcp
```

3. Choose **OAuth** for authentication and complete the authorization flow when prompted.
4. In each chat where you want to use Xata, click the **+** button and enable the Xata connector under **Add sources**.

### Codex CLI

Add the Xata server:

```bash theme={null}
codex mcp add xata --url https://api.xata.tech/mcp
```

<Note>
  The `add` command may open a browser and report an OAuth error. If that happens, continue with the login command below; the `xata` server entry has already been saved.
</Note>

Authenticate with Xata using explicit OAuth scopes:

```bash theme={null}
codex mcp login xata --scopes mcp-client,offline_access
```

Complete authorization in the browser. The `offline_access` scope allows Codex to refresh its Xata session without requiring another browser authorization.

Then start `codex`, run `/mcp`, and verify that `xata` is connected and authenticated.

### Antigravity CLI

Add Xata to your global MCP configuration:

```json ~/.gemini/config/mcp_config.json theme={null}
{
  "mcpServers": {
    "xata": {
      "serverUrl": "https://api.xata.tech/mcp"
    }
  }
}
```

To enable Xata only for one project, use `.agents/mcp_config.json` in that project's root instead.

Start `agy` and enter `/mcp`. In the MCP Manager, use **Authenticate** for `xata` and follow the prompts to complete OAuth.

### OpenCode

Add the Xata server to your OpenCode configuration file:

```json ~/.config/opencode/opencode.json theme={null}
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "xata": {
      "type": "remote",
      "url": "https://api.xata.tech/mcp"
    }
  }
}
```

Then authenticate from your terminal:

```bash theme={null}
opencode mcp auth xata
```

### Amp

Add the server from your terminal:

```bash theme={null}
amp mcp add xata https://api.xata.tech/mcp
```

Then start `amp` — you should be prompted to authenticate in the browser. Run `/mcp list tools` to confirm the server is connected.

### Windsurf

1. In Windsurf, open the Cascade panel and click the MCP (hammer) icon, then **Configure** to open the raw configuration file (`~/.codeium/windsurf/mcp_config.json`).
2. Add the Xata server entry:

```json ~/.codeium/windsurf/mcp_config.json theme={null}
{
  "mcpServers": {
    "xata": {
      "serverUrl": "https://api.xata.tech/mcp"
    }
  }
}
```

3. Save the file and click **Refresh** in the Cascade sidebar. Complete the OAuth flow when the browser window opens.

### Zed

1. Open **Settings** → **AI** → **MCP Servers** and click **Add Server** → **Add Remote Server**, or edit your settings file directly:

```json settings.json theme={null}
{
  "context_servers": {
    "xata": {
      "url": "https://api.xata.tech/mcp"
    }
  }
}
```

2. Zed prompts you to authenticate against the server using the standard MCP OAuth flow.

### Cline

1. Open Cline in VS Code and click the **MCP Servers** icon.
2. In the **Remote Servers** tab, enter `xata` as the name, `https://api.xata.tech/mcp` as the URL, and choose **Streamable HTTP** as the transport. Or edit the configuration JSON directly:

```json theme={null}
{
  "mcpServers": {
    "xata": {
      "type": "streamableHttp",
      "url": "https://api.xata.tech/mcp"
    }
  }
}
```

<Note>
  The transport type must be `streamableHttp` (camelCase). Omitting it causes Cline to fall back to the legacy SSE transport, which the Xata MCP server does not support.
</Note>

### Other MCP clients

Any MCP client can connect if it supports:

* Remote MCP servers over **Streamable HTTP** (not SSE)
* **OAuth with dynamic client registration**, or **custom HTTP headers** for API key authentication

Consult your client's documentation for where to configure remote MCP servers, and use `https://api.xata.tech/mcp` as the URL.

## Verify the connection

After connecting, ask your assistant:

> Use the Xata MCP server to find the REST API operation for listing branches.

The assistant should call `search_operations` with `{"query":"list branches"}` and return the `listBranches` operation, which can be invoked through `call_read_operation`. If it does, the connection is working.

## Available tools

The Xata MCP server exposes the following tools:

| Tool                         | Description                                                                                                               |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| `search_operations`          | Find a Xata REST API operation by intent (for example, "list branches" or "invite member").                               |
| `describe_operation`         | Return the parameters and request/response schemas for a specific operation.                                              |
| `call_read_operation`        | Invoke a read-only Xata REST API operation.                                                                               |
| `call_write_operation`       | Invoke a Xata REST API operation that creates or updates data.                                                            |
| `call_destructive_operation` | Invoke a Xata REST API operation that destroys data or revokes access. Requires `confirm=true`.                           |
| `run_sql`                    | Run SQL against a branch. Read-only by default; statements that mutate data require both `write=true` and `confirm=true`. |
| `describe_schema`            | List the tables and columns of a branch.                                                                                  |
| `list_skills`                | List the available Xata skills — guided workflows for common multi-step tasks.                                            |
| `get_skill`                  | Read the instructions for a specific skill.                                                                               |
| `search_xata`                | Search the Xata documentation.                                                                                            |
| `query_docs_filesystem_xata` | Read Xata documentation pages by path.                                                                                    |

## Security

* Prefer OAuth for interactive clients; tokens are short-lived and can be revoked by disconnecting the server in your client.
* For automation, use a dedicated [API key](/docs/platform/api-key) and rotate it regularly.
* Some tools can modify your data: `call_write_operation` and `call_destructive_operation` can change or delete resources (the latter requires `confirm=true`), and `run_sql` can mutate data when called with both `write=true` and `confirm=true`. Review the actions your assistant proposes before approving them, and keep a human in the loop for any write or delete.

## Troubleshooting

**Authentication keeps failing or loops.** Remove the Xata server from your client, restart the client, and add the server again to trigger a fresh OAuth flow.

**The server connects but no tools show up.** Make sure you completed the authentication step — most tools require a valid session before they appear. Re-run your client's authentication flow, then refresh its tool list. See [Available tools](#available-tools) for the full set.

**Your client can't connect at all.** Confirm the URL is exactly `https://api.xata.tech/mcp` and that your client supports Streamable HTTP. SSE-only clients are not supported.

**The server doesn't appear in your client.** Check the client's MCP configuration file syntax — the JSON shape differs between clients (`mcpServers` vs `servers` vs `context_servers`, `url` vs `serverUrl`) — and check the client's logs. Most clients require a full restart after configuration changes.
