# Ravens

Ravens connects Claude to the Ravens MCP server by Edda Labs so you can act on the Midnight blockchain from a conversation. Claude prepares actions (today: a NIGHT transfer on Preview) and tracks their status. Every action that moves funds returns an approval link: you open it in the Ravens web app, check the details, and approve with your own Midnight wallet. Ravens never asks for your seed phrase or private keys.

## Setup

```
/plugin install ravens --marketplace eddalabs/ravens-marketplace
```


Install the plugin. By default it connects to `https://ravens.eddalabs.io/mcp`. To use a local server, set `RAVENS_MCP_URL` before starting Claude, for example `RAVENS_MCP_URL=http://localhost:8787/mcp`.
