# Ravens

Ravens connects Claude to the Ravens MCP server by Edda Labs so you can work with your Midnight wallet from a conversation:

- Claude can **ask narrow questions** about a wallet you linked ("do I hold at least 2 NIGHT?") and gets only the answer, at the disclosure level you chose.
- Claude can **prepare actions** (today: a NIGHT transfer). Every action that moves funds returns an approval link: you open it in the Ravens web app, check the details, and approve with your own wallet.

Ravens never asks for your seed phrase or private keys.

## Setup

```
/plugin install ravens@ravens-marketplace
```

By default it connects to `https://ravens.eddalabs.io/mcp`; run `/mcp` to sign in with your Ravens account. To use a local server, set `RAVENS_MCP_URL` before starting Claude, for example `RAVENS_MCP_URL=http://127.0.0.1:3211/mcp`.

See [wallet access](../../docs/wallet-access.md) and [connecting Claude](../../docs/connecting-claude.md).
