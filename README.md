# Ravens marketplace

The Claude plugin marketplace for [Ravens](https://ravens.eddalabs.io) by Edda Labs.

## Install

In Claude Code:

```
/plugin marketplace add eddalabs/ravens-marketplace
/plugin install ravens@ravens-marketplace
```

## What's inside

| Plugin | What it does |
| --- | --- |
| `ravens` | Connects Claude to the Ravens MCP server. Claude prepares Midnight actions and tracks them. You approve every action that moves funds in the Ravens web app with your own wallet. |

Ravens never asks for your seed phrase or keys.

## Status

Proof of concept. The hosted server at `https://ravens.eddalabs.io/mcp` is not live yet, so run the MCP server locally from the [ravens](https://github.com/eddalabs/ravens) repo (full guide: [docs/running-locally.md](https://github.com/eddalabs/ravens/blob/main/docs/running-locally.md)).

Today it can prepare one action: an unshielded NIGHT transfer on a local standalone network (`main` branches) or Midnight Preview (`preview` branches).

## Connect to a local server

**Claude Code: plugin with the `using-ravens` skill**

```
RAVENS_MCP_URL=http://localhost:8787/mcp claude --plugin-dir ravens-marketplace/plugins/ravens
```

or without the plugin:

```
claude mcp add --transport http ravens http://localhost:8787/mcp
```

**Claude Desktop / claude.ai: custom connector**

Connectors are called from Anthropic's cloud, so a local server needs a public URL:

```
cloudflared tunnel --config /dev/null --url http://localhost:8787
```

Then go to Settings → Connectors → Add custom connector and enter `https://<printed-host>.trycloudflare.com/mcp`. No auth is needed. The quick-tunnel URL changes on every restart, and anyone with it can reach your server, so stop it when you're done. `--config /dev/null` avoids picking up another tunnel's `~/.cloudflared/config.yml`, whose catch-all answers 404.

## License

Apache-2.0
