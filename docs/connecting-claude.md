# Connecting Claude

All transports expose the same tools. None of them can sign: transactions always go through an approval link.

| Tool | What it does |
| --- | --- |
| `list_wallets` | Your linked wallets and whether their companion tab is online. No addresses or balances. |
| `ask_wallet` | Ask one question about a wallet (see [wallet-access.md](wallet-access.md)). |
| `get_question` | Fetch the answer to a question that was waiting for your wallet. |
| `list_actions` | What Ravens can prepare. |
| `prepare_transfer` | Prepare a NIGHT transfer and return its approval link. |
| `get_request_status` | Status of a prepared request, with the transaction id once submitted. |

## Hosted (coming)

- **claude.ai / Claude Desktop:** Settings → Connectors → Add custom connector → `https://ravens.eddalabs.io/mcp` → Connect, and sign in with your Ravens account.
- **Claude Code:** install the plugin from this marketplace, then run `/mcp` to sign in.

## Local development

Ravens runs locally on one machine: a standalone Midnight network, a local Convex backend and the web app. Setup is in the private `ravens` repo (`docs/running-locally.md`). Test the MCP with local clients; claude.ai can't reach `127.0.0.1`.

- **Claude Code over HTTP:**
  ```
  RAVENS_MCP_URL=http://127.0.0.1:3211/mcp claude --plugin-dir ravens-marketplace/plugins/ravens
  ```
  or `claude mcp add --transport http ravens http://127.0.0.1:3211/mcp`, then `/mcp` to sign in.
- **MCP Inspector:** `npx @modelcontextprotocol/inspector`, Streamable HTTP, `http://127.0.0.1:3211/mcp`.
- **Claude Code over stdio:** the `ravens-mcp` CLI from the `ravens` repo: `ravens-mcp login` once, then `claude mcp add ravens -- ravens-mcp serve`.
