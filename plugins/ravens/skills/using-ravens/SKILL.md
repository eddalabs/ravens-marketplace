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

1. `list_actions` to see what Ravens can do (transfers, programs, questions).
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

## Programs: a payment that runs by itself

When the user wants something to happen later or repeatedly without approving each time ("send Ana 5 NIGHT every Friday", "pay rent on the 1st"), prepare a program with `prepare_program`. A program is one transfer, a schedule, optional conditions, and bounds on when it stops. The user reads it on the approval page and approves it once with their passkey. After that their agent account, a small separate account they fund and limit in Ravens Settings, makes each payment on its own.

- `trigger`: `{ once: true }`, `{ every: "day" }`, `{ every: "week", on: "friday" }` or `{ every: "month", on: 1 }` (day 1 to 28). Schedules are in UTC.
- `transfers`: who each run pays and how much, by `contact` name or `recipient` address, in NIGHT.
- `conditions` (optional): `{ keeps: { atLeast: "100" } }` (the paying account must still hold that much NIGHT after paying), `{ recipients: "contacts" }`, `{ between: { from, to } }`. A run whose conditions don't hold is skipped.
- `bounds`: `max_runs` (the most payments ever) and `until` (the last day, at most a year away). Both are required, so confirm them with the user.

Give the user the `approval_url` and read them the `reads_as` sentences from the result, so they know what they will see. Then:

- `get_program_status` returns `needs_user`, `active`, `paused` or `ended`, and nothing else. You are never told whether a run happened, paid or was skipped, or how much the account holds. Don't guess; the program's page in Ravens shows the user every run.
- A program can't be edited. To change one, the user ends it on its page and you prepare a new one.
- Only the user can approve, pause, resume or end a program, in Ravens.
- A program needs an agent account that is funded and whose limits allow each payment. If the user has none, the approval page says so and Settings is where they create one. Programs run while Ravens is open in a browser where that account is.

For a single payment now, use `prepare_transfer`, not a one-off program.
