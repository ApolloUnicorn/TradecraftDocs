---
description: Provide liquidity to earn a portion of fees from trading activity.
icon: arrow-turn-down
---

# Guide: How to Provide Liquidity

Providing liquidity to Tradecraft pools allows you to earn a portion of trading fees that are generated from all trading activity on that pool.

You can check current pool APRs (based on activity from the last 24 hours) at [tradecraft.fi/pools](https://tradecraft.fi/pools).

{% stepper %}
{% step %}
### Use a supported wallet.

See [wallet-support.md](wallet-support.md "mention") for a full list.
{% endstep %}

{% step %}
### Decide token amounts to pool.

When providing liquidity to a pool, you'll need to deposit both assets in roughly the same amount (in USD value) to the pool.

Visit [tradecraft.fi/pools](https://tradecraft.fi/pools) and click "Add Liquidity" ("+" on mobile) for the pool you'd like to provide liquidity to. You'll see the interface below:

<figure><img src=".gitbook/assets/Screenshot 2026-01-29 at 4.44.44 PM.png" alt=""><figcaption></figcaption></figure>

Enter an amount of either token to pool and the interface will show you how much of the other token you will need to match that contribution, and also an estimate\* of how many Liquidity Provider (LP) tokens you will receive as a receipt to represent your share of the pools assets.

If you only have one of the two tokens in your wallet, you can use Tradecraft to trade some of your holdings for the token you're missing ([guide-how-to-trade.md](guide-how-to-trade.md "mention")).

Adjust the amounts of the two tokens to be deposited until they match the total amount you'd like to contribute.

{% hint style="warning" %}
**\*Important**

Amounts shown are not a guaranteed promise of return. Factors like protocol use and market movements, among others, may result in receiving a different number of tokens than the shown estimate.
{% endhint %}
{% endstep %}

{% step %}
### Send & receive.

From your wallet, offer a transfer of both tokens to the Liquidity Address shown in the interface.

{% hint style="info" %}
**Excess Tokens**

If you send too many or too few of either token, Tradecraft will accept the amounts available in the correct ratio, and automatically return the rest to you along with the LP tokens.
{% endhint %}

In less than a minute you should receive a return offer of LP tokens, named "TC `SYMBOL`/`SYMBOL` LP".  In most wallets you will need to manually accept the incoming transfer.

The LP tokens represent your share of the liquidity pool. To remove your liquidity, send the LP tokens back to the same Liquidity Address. This will initiate the return of your share of the pool's underlying tokens. Again, remember to accept the incoming transfer offer of the two tokens.
{% endstep %}
{% endstepper %}

Questions? Check out our [frequently-asked-questions.md](frequently-asked-questions.md "mention") page.
