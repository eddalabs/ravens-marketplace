---
name: using-ravens
description: Use when the user wants to send, swap, move or check assets on Midnight through Ravens, or asks about a Ravens action, approval link or agent.
---

<!-- Generated from packages/mcp-core/src/guidance.ts in eddalabs/ravens. Change it there, not here. -->

# Using Ravens

Ravens lets the user act on Midnight from this conversation. You plan and prepare; the user approves.

## Rules

- Never ask for, accept or store a seed phrase, private key, viewing key or password. If the user offers one, tell them not to share it.
- You never sign. Every action that moves funds returns an approval link (`approval_url`) from the Ravens MCP server. Give the user that link and tell them to check the details on the page before approving.
- Describe an action from the tool result (summary, amounts, network, expiry), never from memory or estimates.
- Nothing moves until the user approves. Requests expire after 30 minutes: if one expired, prepare a new one.

## Typical flow

1. `list_actions` shows what Ravens can do: a fixed list of actions (today, transfers, and moves between the user's wallet and their worker). Each action says what it needs. Use it when the user asks "what can I do?", and answer in a sentence or two, in plain words.
2. Confirm the details with the user. For a transfer: the recipient, as an address or the name of one of their contacts, and the amount. Don't ask which account pays (see below).
3. Call the matching prepare tool: `prepare_transfer` for one recipient, `prepare_batch_transfer` for 2 to 25 recipients in one approval (payouts, refunds, splitting a bill), `prepare_move` between the user's own accounts. For any other action from the list, `describe_action` shows what it takes and `prepare` prepares it. Share the `approval_url` it returns.
4. If it returns `setup_needed`, nothing was prepared: the user first creates their passkey in the Ravens app, at the link in the result. Then prepare it again.
5. When the user says they approved, call `get_request_status` and report the status.

When the user names a person instead of an address ("send Ana 5"), pass the name as `contact` and never ask for the address. Their device turns the name into an address when they approve, where they see both. You cannot list contacts or see addresses.

## What you are told about a request

Requests are sealed so that only the user's own devices can read them. You get a status and nothing else:

- `needs_user`: it waits for the user. They may need to approve it, or to sort out something only they can see (not enough funds, a contact name that doesn't match). You are not told which, so don't guess: send them to the approval page.
- `pending`: approved and being sent.
- `done`, `rejected`, `failed`, `expired`: final. After `failed` or `expired`, offer to prepare a new request.

You are not given the transaction hash, addresses or balances. The user sees the transaction on the approval page and in their wallet.

## What you cannot ask

There is nothing to ask Ravens about the user. You cannot see or check a balance, whether they hold a token, their contacts, their addresses, or which applications they use.

- If the user asks how much they hold, say you can't see amounts and that the Ravens app shows it.
- Don't check before you prepare. Prepare the request the user asked for: whether they can afford it, and whether a name is one of their contacts, is found out on their device, where only they see the reason.
- A request that can't go ahead stays at `needs_user`. Send the user to the approval page; don't guess why.

## Workers: who carries a request out

The user's own workers carry out what you prepare. A worker is an account of theirs that can pay: one inside Ravens, approved with a tap of their passkey, or their own wallet (Lace, 1AM), approved in the wallet's window. Which one acts is decided on the user's device. You don't choose it and you are never told.

- Prepare the request without naming a worker. Don't ask the user which account pays: that question is gone.
- Only when the user names one themselves ("use Savings", "pay from my wallet") pass their words as `worker`, exactly as they said them. Their device works out which worker that is. You can't list workers or check a name.
- A named worker is tried first. Pass `worker_mode: "only"` when the user said it must be that one and no other.
- Once the user has named a worker for the conversation ("use my wallet for all of these"), keep passing it until they say otherwise.
- You never see an address, how many workers there are, or what any of them holds, and you never need to.

If the worker can't pay (not enough funds, no fee balance yet, a name that matches none of theirs), only the user sees why, when they open the request. There they can try again, reject, or choose another worker. You are not told which happened and you stay at `needs_user`: send them to the approval page, and don't prepare a second request for the same payment unless they ask.

## Moving funds between the two

- To move NIGHT between the user's own accounts, call `prepare_move`, not `prepare_transfer`, and never ask for an address: their device knows both accounts. `from: "worker"` moves it back to their wallet (`amount: "all"` empties the worker). `from: "wallet"` adds funds to the worker, and needs an amount.
- A move is sent by the account it leaves, and approved the way that account approves: a passkey tap out of the worker, the wallet's own window out of the wallet.
- A new user's worker exists from the start and is empty. If they want to pay from it, offer to add funds first.

## Retries and refusals

- When a prepare call fails with a timeout or an error and you call it again, give both calls the same `idempotency_key` (a short name of your own for the request). The second call then returns the request already made instead of a second one. Use a new name for a new request.
- A refusal carries a code beside its words: `account_not_named` (a move needs `from`: ask the user), `malformed` (the words say what to change), `unsupported_asset`, `too_many_outputs` (split the list), `unknown_action`, `not_found`, `unavailable` (try again in a moment). Decide by the code; tell the user the words.

## One action at a time

Ravens does single actions that the user approves. There are no schedules, repeating transactions or conditions ("every Friday", "only if my balance is above 100"). If the user asks for one, say Ravens doesn't do that and offer to prepare it now.
