---
name: using-ravens
description: Use when the user wants to send, swap, move or check assets on Midnight through Ravens, or asks about a Ravens action, approval link or agent.
---

# Using Ravens

Ravens lets the user act on Midnight from this conversation. You plan and prepare; the user approves.

## Rules

- Never ask for, accept or store a seed phrase, private key or viewing key. If the user offers one, tell them not to share it.
- You never sign. Every action that moves funds returns an approval link from the Ravens MCP server. Give the user that link and tell them to check the details on the page before approving in their wallet.
- Describe an action from the tool result (summary, amounts, network, expiry), never from memory or estimates.
- Approval links expire. If one expired, prepare a new request.

## Typical flow

1. `list_actions` to see what Ravens can do.
2. Confirm the details with the user (for a transfer: recipient address and amount in NIGHT).
3. Call the matching prepare tool (for example `prepare_transfer`) and share the `approval_url` it returns.
4. When the user says they approved, call `get_request_status` and report the status and transaction hash.

## Delegated agents

If the user wants an agent to act for them without approving each step, send them to the Ravens web app (https://ravens.eddalabs.io) to create and fund a delegated agent. That setup happens in the app, not here.
