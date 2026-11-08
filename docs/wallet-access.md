# Wallet access

Ravens gives agents **read-only, question-by-question** access to your Midnight wallet. Agents never receive your balances, addresses or history in bulk, and they can never move funds: every transaction goes through an approval link that you open and approve in your own wallet.

## Linking a wallet

1. Sign in at the Ravens web app and open **/wallet**.
2. **Connect a wallet** with the DApp Connector (Lace, 1AM, …).
3. **Link this wallet.** Some wallets ask you to sign a short text. That's an extra ownership check; it can't move funds.

Your wallet address is stored encrypted, under a key that belongs to your account only. Deleting your account destroys that key, which makes everything stored for you unreadable.

## The companion tab

Questions are answered **in your browser**, by the /wallet tab, while it's open and the wallet is connected. The tab reads the wallet, works out the answer, and sends back only that answer. The full data never leaves your browser.

- The pill at the top shows **Answering** while the tab is live. **Pause** stops it answering.
- When the tab is closed, the wallet is locked, or the extension dropped the connection, the agent gets a link instead of an answer. Opening it (and reconnecting the wallet if asked) answers the question, and the agent picks the answer up.
- Wallets differ in how long they stay connected. If the pill says the wallet disconnected, click **Reconnect**.

## What agents may learn

Each fact has a disclosure level you set under **What agents may learn**:

| Level | An agent can ask | Example answer |
| --- | --- | --- |
| Yes/no (default) | Comparisons only | "Is your NIGHT balance at least 2?" → **Yes** |
| Range | A rough bucket | "1 to 10 NIGHT" |
| Exact | The value | "5.25 NIGHT" |

A question above your level is refused, and the agent is told to ask a comparison instead.

Facts available today:

| Fact | What it answers |
| --- | --- |
| `balance` | NIGHT (or another token) held, shielded and/or unshielded. |
| `can_afford` | Whether the wallet can send an amount now. DUST fees are sponsored, so they aren't counted. |
| `activity` | How many transactions the wallet has made, if the wallet reports it. |

Every question and answer is logged in the **feed** on /wallet, with the agent that asked.

## Transactions

When an agent prepares a transfer, it gives you a link to `/approve/<id>`. The page shows the agent, its reason, the recipient and the amount. Approve it with your wallet, or reject it. Links expire after 30 minutes.
