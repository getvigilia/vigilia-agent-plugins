# Vigilia for AI coding agents

[Vigilia](https://getvigilia.com/?utm_source=github&utm_medium=repo) watches your live app, explains every problem in plain language and writes a fix prompt for it. Your coding agent reads the incident over MCP and fixes the cause in your project. Vigilia then watches real traffic and confirms the fix held.

This repository holds the setup for every agent that can use Vigilia: the MCP server address, two skills (`fix` and `install`), a Cursor plugin, a Gemini CLI extension and Cline's install guide. It is configuration and instructions only, no product code. The server is hosted by Vigilia:

```
https://app.getvigilia.com/mcp
```

It signs in with OAuth, so no token is copied around. You need a Vigilia account, free for one app.

## Connect your agent

Whichever way you add it, the first time you use it your agent sends you to a Vigilia page: keep *Read & write* (so the agent can mark an incident fixed) and choose **Allow**. The same page lists and revokes connections later, in Vigilia under Settings, Connected AI. For one-click links and copyable configs, open [app.getvigilia.com/connect](https://app.getvigilia.com/connect).

**Claude Code**

```
claude mcp add --transport http --scope user vigilia https://app.getvigilia.com/mcp
```

Then type `/mcp`, choose `vigilia` and Authenticate. The Claude Code plugin, which adds `/vigilia:fix` and `/vigilia:install`, lives in [getvigilia/vigilia-claude-plugin](https://github.com/getvigilia/vigilia-claude-plugin).

**Cursor**

To get the server, both skills and a rule in one step, copy this repository to `~/.cursor/plugins/local/vigilia` and run *Developer: Reload Window*. To add only the server, put this in `~/.cursor/mcp.json`, or `.cursor/mcp.json` in a project:

```json
{ "mcpServers": { "vigilia": { "url": "https://app.getvigilia.com/mcp" } } }
```

Then open Cursor Settings, MCP, and click **Connect** next to `vigilia`.

**VS Code**

Add this to `.vscode/mcp.json` in your project (or run *MCP: Add Server* from the command palette), then start the server when VS Code offers to:

```json
{ "servers": { "vigilia": { "type": "http", "url": "https://app.getvigilia.com/mcp" } } }
```

**Windsurf**

Open Settings, Cascade, MCP servers, *View raw config*, add this and refresh:

```json
{ "mcpServers": { "vigilia": { "serverUrl": "https://app.getvigilia.com/mcp" } } }
```

**Cline**

Add a remote server with transport **Streamable HTTP**, or follow [llms-install.md](llms-install.md), which Cline can read and follow on its own.

**Gemini CLI**

```
gemini extensions install https://github.com/getvigilia/vigilia-agent-plugins
```

Then run `/mcp auth vigilia`. To add only the server: `gemini mcp add --transport http vigilia https://app.getvigilia.com/mcp`.

**Claude and Claude Desktop**

In a chat, click **+**, then Connectors, Add connector, Browse connectors, and search for Vigilia. Can't find it? Choose **Add custom connector** and paste the server address above.

**Lovable and Bolt**

Lovable: Settings, Connectors, Chat connectors, add a custom MCP server. Bolt: Connectors, *Custom MCP server*, transport HTTP. Paste the server address, choose OAuth and allow it on the Vigilia page.

**Any other agent, skills only**

```
npx skills add getvigilia/vigilia-agent-plugins
```

installs the two skills for the agents it finds. Connect the server separately, as above.

## Try asking

- Use Vigilia to fix my worst incident.
- What's broken in my app right now, according to Vigilia?
- Did the last fix I made actually hold?
- Install Vigilia in this project.

## The skills

- **`fix`**: takes your worst open incident (or the one you name), reads Vigilia's diagnosis and evidence, fixes the cause in your code, and hands it back with `mark_fixed` once it is deployed. Vigilia then reports whether the fix held.
- **`install`**: adds Vigilia's script tag to your project in the right place for the framework, updates an existing Content-Security-Policy (never invents or loosens one), and verifies the live install.

## The tools

Seven only read: `list_apps`, `list_open_incidents`, `get_incident`, `get_app_health`, `get_install_instructions`, `get_server_install_instructions`, `get_fix_status`.

Four write to Vigilia's own records, never to your site or code: `add_app` creates an app record and starts uptime checks on its address; `create_server_key` makes the secret key for reporting backend errors (shown once; Pro plan); `mark_fixed` marks an incident fixed so Vigilia can watch for it to come back; `check_install` fetches your live site's public pages and records the result on the app. With read-only access, the first three are unavailable.

## What this repository runs and sends

- **Nothing runs on your machine by itself.** There are no hooks, scripts or binaries here: the Cursor plugin and the Gemini extension declare one MCP server, and carry two skills, a rule or context file, and a logo. The skills are plain instructions to your agent.
- **One network destination for the plugin itself:** `https://app.getvigilia.com/mcp`. Your agent keeps the OAuth token in its own credential store.
- **Edits happen in your project, in your session.** `install` adds Vigilia's script tag for you to review and commit; `fix` changes whatever your code needs. Nothing is deployed for you.
- **The script tag talks to Vigilia too.** Once you deploy it, your site loads `vigilia.js` from `https://cdn.getvigilia.com`, and your visitors' browsers send error reports back to Vigilia, scrubbed of emails, tokens and card numbers before they are stored.

Vigilia never sees your code. What it stores about your account and apps is in its [privacy policy](https://getvigilia.com/privacy). Questions: support@getvigilia.com.

## License

MIT, see [LICENSE](LICENSE).
