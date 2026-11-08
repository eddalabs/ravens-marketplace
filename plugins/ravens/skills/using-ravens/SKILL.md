---
name: using-ravens
description: Use when the user wants to check something about their Midnight wallet (balance, whether they can afford something, activity), send or move assets on Midnight through Ravens, or asks about a Ravens action, approval link or linked wallet.
---

# Using Ravens

Ravens lets the user work with their Midnight wallet from this conversation. You can ask narrow questions about a linked wallet and prepare actions; the user approves every action in their own wallet.

## Rules

- Never ask for, accept or store a seed phrase, private key or viewing key. If the user offers one, tell them not to share it.
- You never sign. Every action that moves funds returns an approval link. Give the user that link and tell them to check the details on the page before approving in their wallet.
- Ask the narrowest question that answers the user's need. Prefer a comparison ("at least 2 NIGHT?") over asking for a value. If a question is refused, the user chose a stricter disclosure level: ask a comparison instead, don't retry for the value.
- Describe results from the tool output (answers, summary, amounts, network, expiry), never from memory or estimates.
- Approval links expire after 30 minutes. If one expired, prepare a new request.

## Wallet questions

1. `list_wallets` to see the linked wallets and whether their companion tab is online.
2. `ask_wallet` with one question, for example `{ fact: "balance", op: ">=", value: "2" }` or `{ fact: "can_afford", params: { amount: "5" }, op: "value" }`.
3. If the status is `waiting_for_wallet`, give the user the link: opening it (with their wallet unlocked) answers the question. When they say it's done, call `get_question`.

DUST fees are sponsored by Ravens, so don't ask about DUST.

## Transfers

1. `list_actions` to see what Ravens can do.
2. Confirm the recipient address and amount in NIGHT with the user. If useful, check `can_afford` first.
3. `prepare_transfer` and share the `approval_url` it returns.
4. When the user says they approved, call `get_request_status` and report the status and `tx_id`.
