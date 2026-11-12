---
name: using-ravens
description: Use when the user wants to send, swap, move or check assets on Midnight through Ravens, or asks about a Ravens action, approval link or agent.
---

# Using Ravens

Ravens lets the user act on Midnight from this conversation. You plan and prepare; the user approves.

## Rules

- Never ask for, accept or store a seed phrase, private key or viewing key. If the user offers one, tell them not to share it.
- You never sign. Every action that moves funds returns an approval link from the Ravens MCP server. Give the user that link and tell them to check the details on the page before approving.
- Describe an action from the tool result (summary, amounts, network, expiry), never from memory or estimates.
- Approval links expire. If one expired, prepare a new request.

## Typical flow

1. `list_actions` to see what Ravens can do: a fixed list of actions (today, transfers) and the two questions. Each action says what it needs. Use it when the user asks "what can I do?".
2. Confirm the details with the user (for a transfer: the recipient, as an address or the name of one of their contacts, and the amount in NIGHT).
3. Call the matching prepare tool (for example `prepare_transfer`) and share the `approval_url` it returns. If it returns `setup_needed`, nothing was prepared: the user first creates their passkey in the Ravens app, at the link in the result. Then prepare it again.
4. When the user says they approved, call `get_request_status` and report the status.

## What you are told about a request

Requests are sealed so that only the user's own devices can read them. You get a status and nothing else:

- `needs_user`: it waits for the user. They may need to approve it, or to sort out something only they can see on the approval page (not enough funds, a contact name that doesn't match). You are not told which, so don't guess: send them to the approval page.
- `pending`: approved and being sent.
- `done`, `rejected`, `failed`, `expired`: final. After `failed` or `expired`, offer to prepare a new request.

You are not given the transaction hash, addresses or balances. The user sees the transaction on the approval page and in their wallet.

## Asking a question

`ask_wallet` answers two things, each with a yes or a no:

- `token`: does the user hold any of this token? Use `"NIGHT"` or a token type (64 hex characters).
- `contact`: is this name one of the user's contacts? Use it to check a name before preparing a transfer to it.

Give exactly one of the two. There is nothing else to ask: no balance, no amount, no "at least". If the user asks how much they hold, say you can't see amounts and that their wallet shows it. Don't try to work out an amount from several questions.

What comes back:

- `answered`, with `answer` "Yes" or "No".
- `waiting_for_device`: none of the user's devices answered. Give them the `link`; once Ravens is open and unlocked there it answers by itself. Then call `get_question` with the `id`.
- `collected`: you already read this answer and Ravens has erased it. Ask again if you need it.
- `setup_needed` or `no_wallet`: the user first creates their passkey, or connects a wallet, at the link in the result.

## Who sends it, and who authorizes

The user may have more than one thing that can carry out a request: the wallet they connected, and an agent account (a wallet inside Ravens that they fund). You don't choose between them and you are not told which was used. The user's own device matches what the action needs to what each one can do and holds, and the approval page tells the user who sends it and how they authorize: with their passkey when the agent account sends, in their wallet's own prompt otherwise. Don't ask the user which wallet to use, and don't guess which one sent.

## One action at a time

Ravens does single actions that the user approves. There are no schedules, repeating payments or conditions ("every Friday", "only if my balance is above 100"). If the user asks for one, say Ravens doesn't do that and offer to prepare the payment now.
