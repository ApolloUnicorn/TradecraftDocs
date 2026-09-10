---
description: Guarantee every trade results in an expected minimum amount of tokens back.
icon: scale-balanced
---

# Guide: Specifying Minimums

When making a trade with Pool Addresses you may specify a minimum number of tokens to receive in return for your trade. If the minimum cannot be met, the tokens you attempt to trade will be returned.

### **Short Instructions**

1. Generate a fresh quote for a trade using the interface at [tradecraft.fi](https://tradecraft.fi).
2. Copy the number of tokens quoted for the trade and multiply it by a factor of your choosing to account for slippage. For example, multiply by `0.97` to account for 3% slippage. That will be the minimum number of tokens you expect back.
3. When preparing the transfer of tokens to the Tradecraft Pool Address, enter the minimum number in the memo field. Then send the transfer.
4. Your trade will either execute and you'll receive at least the minimum number of tokens — or more if the current price allows — or it will fail because the order cannot be completed at that price, and your tokens will be returned.

### **Step by Step Example**

{% stepper %}
{% step %}
### Generate a fresh trade quote.

Using the interface at [tradecraft.fi](https://tradecraft.fi), see what the current expected return amount is for your desired trade:

<figure><img src=".gitbook/assets/Screenshot 2026-06-05 at 2.43.17 PM.png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Calculate your desired minimum.

Slippage is a term used to describe what can happen between the time you generate a quote and the time your order gets processed on-chain. In that time orders from other traders can be executed, affecting the balance of tokens in the pool and moving the price. Accounting for slippage reduces the chance of your trade being rejected due to price drift.

Commonly used slippage tolerances range from 5% to 0.25% depending on the TVL of the pool. Token balance (and therefore token price) in pools with smaller TVL is affected more by a trade than it is in pools with large TVL.

The formula to calculate the minimum is:\
\
`quote x (1 - slippage/100) = minimum`

For this example, we'll use 3% slippage tolerance:

`14.2742668747 x (1 - 3/100) = minimum`

Simplified, this equation is:

`14.2742668747 x 0.97 = 13.8460388685`&#x20;
{% endstep %}

{% step %}
### Send tokens, with minimum in memo field.

While following the instructions on the quote interface to send tokens to execute your trade, enter the minimum you'd like to receive in the memo field of the transfer.

{% tabs %}
{% tab title="Console Wallet" %}
<div><figure><img src=".gitbook/assets/Screenshot 2026-06-05 at 3.09.36 PM copy.png" alt=""><figcaption></figcaption></figure> <figure><img src=".gitbook/assets/Screenshot 2026-06-05 at 3.09.55 PM.png" alt=""><figcaption></figcaption></figure> <figure><img src=".gitbook/assets/Screenshot 2026-06-05 at 3.10.19 PM.png" alt=""><figcaption></figcaption></figure></div>
{% endtab %}

{% tab title="Loop Wallet" %}
<div><figure><img src=".gitbook/assets/Screenshot 2026-06-05 at 3.07.54 PM.png" alt=""><figcaption></figcaption></figure> <figure><img src=".gitbook/assets/Screenshot 2026-06-05 at 3.08.15 PM.png" alt=""><figcaption></figcaption></figure></div>
{% endtab %}
{% endtabs %}
{% endstep %}

{% step %}
### Done!

Your trade will be executed at the best possible price available, at or above the minimum specified. If the minimum cannot be met, your tokens will be returned to you instead.
{% endstep %}
{% endstepper %}

### Additional Details

* For a minimum to be honored it must be the first text in the memo field, and have no more than 10 decimal places.
* Minimums may be specified as whole numbers, or decimal numbers.
* You may put additional notes in the memo field after the minimum, separated by a space. For example, `5.25 my extra notes` is a valid memo.
