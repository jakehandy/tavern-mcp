# Installing the Tavern MCP server

Tavern's MCP server is hosted, so there is nothing to clone, build or run. You only add the address to the client's MCP settings.

- Address: `https://gettavern.app/mcp`
- Transport: Streamable HTTP
- Sign-in: OAuth 2.1 with PKCE, or a personal API key sent as a bearer token

The user needs a free Tavern account and a campaign. They can sign up at https://gettavern.app/signin.

## Cline

Add this entry to `cline_mcp_settings.json` (open it from the MCP Servers panel, then Configure MCP Servers):

```json
{
  "mcpServers": {
    "tavern": {
      "type": "streamableHttp",
      "url": "https://gettavern.app/mcp"
    }
  }
}
```

The first connection answers `401` and points the client at Tavern's sign-in. When the client supports OAuth, it opens a Tavern consent page in the browser, where the user signs in and approves the connection.

If the client can't complete the OAuth sign-in, use a personal API key instead. Ask the user to make one in Tavern under Settings, API and assistants (it starts with `tvn_`). Then add it as a header:

```json
{
  "mcpServers": {
    "tavern": {
      "type": "streamableHttp",
      "url": "https://gettavern.app/mcp",
      "headers": { "Authorization": "Bearer tvn_your_key" }
    }
  }
}
```

Never write the user's key anywhere except this settings file.

## Check it works

Call the `whoami` tool, then `list_campaigns`. Campaigns are named by slug, so use a slug from `list_campaigns` with the other tools.

To load fewer tools, add `?toolsets=` to the address, for example `https://gettavern.app/mcp?toolsets=core,characters`. The full tool list is at https://gettavern.app/developers.
