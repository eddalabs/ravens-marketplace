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

Proof of concept. The hosted server at `https://ravens.eddalabs.io/mcp` is not live yet, so point the plugin at a local server (from the [ravens](https://github.com/eddalabs/ravens) repo):

```
RAVENS_MCP_URL=http://localhost:8787/mcp claude
```

Today it can prepare one action: an unshielded NIGHT transfer on Midnight Preview.

## License

Apache-2.0
