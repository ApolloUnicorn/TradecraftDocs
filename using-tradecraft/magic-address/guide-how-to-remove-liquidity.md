---
icon: arrow-turn-up
---

# Guide: How to Remove Liquidity

{% stepper %}
{% step %}
### Use a supported wallet.

See [wallet-support.md](wallet-support.md "mention") for a full list.
{% endstep %}

{% step %}
### Get the correct Pool Address.

Go to [pools-and-pool-addresses.md](pools-and-pool-addresses.md "mention") and copy the "Add/Remove Liquidity" address listed for the pool you'd like to use.
{% endstep %}

{% step %}
### Get returned tokens quote (optional).

Go to the [Liquidity](https://app.gitbook.com/s/CCRi0UxKmgIrQPvrXtbk/routes/liquidity "mention") page and click "Test it" for the "Get LP withdrawal quote" API.

Enter the pool's two token symbols in the space for `tokenA` and `tokenB`.

Enter the number of LP tokens you'd like to exchange in `lpTokenAmount` and press "Send".

The output will show you how many of each of the pool's tokens are _currently expected\*_ in return for the LP tokens.

{% hint style="info" %}
**\*Imporant**

Quoted amounts are not a guaranteed promise of return. Factors like protocol use, among others, may result in receiving a different number of tokens than quoted.
{% endhint %}
{% endstep %}

{% step %}
### Send LP tokens to the Pool Address & receive asset tokens.

From your wallet, send the LP tokens to the Pool Address from step 2.&#x20;

In less than a minute you should receive transfer offers for both of the pool's tokens. Remember to approve the incoming transfers.
{% endstep %}
{% endstepper %}

Questions? Check out our [frequently-asked-questions.md](frequently-asked-questions.md "mention") page.
