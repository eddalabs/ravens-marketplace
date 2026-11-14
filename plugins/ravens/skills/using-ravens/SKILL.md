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

1. `list_actions` shows what Ravens can do: a fixed list of actions (today, transfers and moves between the user's own two accounts) and the questions. Each action says what it needs. Use it when the user asks "what can I do?", and answer in a sentence or two, in plain words.
2. Confirm the details with the user. For a transfer: the recipient, as an address or the name of one of their contacts, the amount in NIGHT, and which of their two accounts pays, wallet or worker (see below).
3. Call the matching prepare tool: `prepare_transfer` for one recipient, `prepare_batch_transfer` for 2 to 25 recipients in one approval (payouts, refunds, splitting a bill), `prepare_move` between the user's own accounts. Share the `approval_url` it returns.
4. If it returns `setup_needed`, nothing was prepared: the user first creates their passkey in the Ravens app, at the link in the result. Then prepare it again.
5. When the user says they approved, call `get_request_status` and report the status.

When the user names a person instead of an address ("send Ana 5"), pass the name as `contact` and never ask for the address. Their device turns the name into an address when they approve, where they see both. You cannot list contacts or see addresses.

## What you are told about a request

Requests are sealed so that only the user's own devices can read them. You get a status and nothing else:

- `needs_user`: it waits for the user. They may need to approve it, or to sort out something only they can see (not enough funds, a contact name that doesn't match). You are not told which, so don't guess: send them to the approval page.
- `pending`: approved and being sent.
- `done`, `rejected`, `failed`, `expired`: final. After `failed` or `expired`, offer to prepare a new request.

You are not given the transaction hash, addresses or balances. The user sees the transaction on the approval page and in their wallet.

## Asking a question

`ask_wallet` answers four things, each with a yes or a no. Give exactly one of:

- `token`: does the user hold any of this token? Use `"NIGHT"` or a token type (64 hex characters).
- `contact`: is this name one of the user's contacts? Use it to check a name before preparing a transfer to it.
- `contract`: does the user use this contract (application) in Ravens? A contract address (64 hex characters).
- `private_data`: is private information kept for this contract? A contract address. A no means an action on that contract that needs the user's private state can't be carried out from Ravens yet.

With `token` you can add `account` (`"wallet"` or `"worker"`) to ask about one of the user's two accounts instead of both together. The two contract questions cover every wallet the user has in Ravens, never say which, and never show the information itself.

There is nothing else to ask: no balance, no amount, no "at least". If the user asks how much they hold, say you can't see amounts and that their wallet shows it. Don't try to work out an amount from several questions. Whether they can afford a transfer is checked on their device when they approve it.

What comes back:

- `answered`, with `answer` "Yes" or "No".
- `not_available`: none of the user's devices answered. Ravens is closed or locked, or the account you asked about is not open anywhere. Give the user the `link`, and call `ask_wallet` again once they say Ravens is open.
- `setup_needed` or `no_wallet`: the user first creates their passkey, or connects a wallet, at the link in the result.

Answers are live. The call waits while the user's device works it out, which can take up to about 45 seconds for a wallet with many tokens, so let it finish. Nothing is stored afterwards: there is no answer to fetch later and nothing to poll. If you need the answer again, ask again.

## The user's two accounts, and which one pays

Every user has two accounts. Tell them apart by where the key lives:

- **Wallet**: the user's own wallet, in Lace or 1AM. Its key is outside Ravens. The user approves in the wallet's own window, on a device where the wallet is installed.
- **Worker**: a wallet inside Ravens that carries out what agents ask, with funds the user gave it. Its key is sealed in their Ravens vault. The user approves with one tap of their passkey, from any device, including a phone.

Every transfer names the account that pays: `from: "wallet"` or `from: "worker"`. That choice is the user's, never yours, and both are equal ways to use Ravens.

- If the user has not said which account, ask before you prepare anything, and explain the difference in a sentence each, as above. Don't pick one for them, don't default to either, and don't guess from the amount.
- Once the user has said which, that holds for the rest of the conversation unless they say otherwise. You don't need to ask again for every payment.
- Ravens never picks either. A transfer without `from` is refused with a reminder to ask.
- You never see either address, which wallet it is, or what either holds, and you never need to.

If the named account can't pay (not enough funds, no fee balance yet, the wallet is not on that device), only the user sees why, when they open the request. There they can try again, reject, or pay from the other account instead. You are not told which happened and you stay at `needs_user`: send them to the approval page, and don't prepare a second request for the same payment unless they ask.

## Moving funds between the two

- To move NIGHT between the user's own accounts, call `prepare_move`, not `prepare_transfer`, and never ask for an address: their device knows both accounts. `from: "worker"` moves it back to their wallet (`amount_night: "all"` empties the worker). `from: "wallet"` adds funds to the worker, and needs an amount.
- A move is sent by the account it leaves, and approved the way that account approves: a passkey tap out of the worker, the wallet's own window out of the wallet.
- A new user's worker exists from the start and is empty. If they want to pay from it, offer to add funds first.
- `ask_wallet` with `token` and `account: "worker"` asks whether the worker holds any of a token, for instance before the user chooses it.
- `ask_wallet` with `token: "DUST"` and `account: "worker"` asks whether the worker can pay a fee. Use it when a request from the worker keeps waiting. The wallet's DUST can't be asked.

## One action at a time

Ravens does single actions that the user approves. There are no schedules, repeating payments or conditions ("every Friday", "only if my balance is above 100"). If the user asks for one, say Ravens doesn't do that and offer to prepare the payment now.
