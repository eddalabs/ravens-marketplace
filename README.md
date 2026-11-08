# Ravens marketplace

The Claude plugin marketplace and user docs for [Ravens](https://ravens.eddalabs.io) by Edda Labs.

Ravens lets Claude act on Midnight for you without ever holding your keys:

- **Ask about your wallet.** Claude asks narrow questions ("do I hold at least 2 NIGHT?") and gets only the answer. You choose how much an agent may learn.
- **Prepare transactions.** Claude prepares an action and gives you an approval link. You check it and approve with your own wallet. Nothing is signed anywhere else.

## Install

In Claude Code:

```
/plugin marketplace add eddalabs/ravens-marketplace
/plugin install ravens@ravens-marketplace
```

## What's inside

| Path | What |
| --- | --- |
| `plugins/ravens` | Connects Claude to the Ravens MCP server, with the `using-ravens` skill. |
| [`docs/wallet-access.md`](docs/wallet-access.md) | How linking a wallet works, what agents can and can't learn, and the companion tab. |
| [`docs/connecting-claude.md`](docs/connecting-claude.md) | Connecting claude.ai, Claude Desktop and Claude Code (HTTP or stdio). |

Ravens never asks for your seed phrase or keys.

## Status

Proof of concept. The hosted server at `https://ravens.eddalabs.io/mcp` is not live yet. Today Ravens runs locally against a standalone Midnight network: see [docs/connecting-claude.md](docs/connecting-claude.md).

## License

Apache-2.0
