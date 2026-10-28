# Install Vigilia in Cline

Vigilia is a hosted MCP server (Streamable HTTP, `https://app.getvigilia.com/mcp`). There is nothing to clone, build or run locally, and no environment variable to set.

## 1. Add the server

Open Cline's MCP settings (MCP Servers icon, then **Configure**, then **Configure MCP Servers**; this opens `cline_mcp_settings.json`) and add this entry under `mcpServers`, keeping any servers already there:

```json
{
  "mcpServers": {
    "vigilia": {
      "type": "streamableHttp",
      "url": "https://app.getvigilia.com/mcp",
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

`"type": "streamableHttp"` matters: without it Cline assumes the older SSE transport and the connection fails.

The same thing from the UI: MCP Servers, **Remote Servers**, name `vigilia`, URL above, transport **Streamable HTTP**, **Add Server**.

## 2. Sign in

Vigilia signs in with OAuth. If Cline shows an authenticate or sign-in action for the `vigilia` server, run it, then choose **Allow** on the Vigilia page that opens. Keep *Read & write*, so the agent can mark an incident fixed once the fix is deployed.

If your version of Cline can't sign in to a remote server, use a token instead:

1. Sign in at https://app.getvigilia.com, open **Settings, API tokens** and create a token.
2. Add it to the server entry: `"headers": { "Authorization": "Bearer <token>" }`.

Never commit the token to a repository.

A Vigilia account is free for one app: https://getvigilia.com/?utm_source=cline&utm_medium=directory&utm_campaign=listings

## 3. Check that it works

Ask: "What's broken in my app, according to Vigilia?" Cline should call `list_apps` and `list_open_incidents`.

- No apps yet? Ask it to add the live site with `add_app`, then to install Vigilia in the project: `get_install_instructions` returns the exact script tag for the framework, and `check_install` verifies the live site once it is deployed.
- Open incidents? Ask it to fix the worst one. It reads the diagnosis and the evidence with `get_incident`, fixes the cause in your project, and after you deploy calls `mark_fixed`. Vigilia then watches real traffic and `get_fix_status` says whether the fix held.

## Tools

Read-only: `list_apps`, `list_open_incidents`, `get_incident`, `get_app_health`, `get_install_instructions`, `get_server_install_instructions`, `get_fix_status`.

Write to Vigilia's own records, never to your site or code: `add_app`, `create_server_key`, `mark_fixed`, `check_install`.

None of them edits your code, your hosting or your deployments. The fixing happens in your Cline session.

Questions: support@getvigilia.com. Privacy policy: https://getvigilia.com/privacy
