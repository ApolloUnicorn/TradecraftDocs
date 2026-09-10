---
icon: question
---

# Frequently Asked Questions

### Having trouble? We offer white glove support.

Email [info@tradecraft.fi](mailto:info@tradecraft.fi) to connect.

### Sending tokens sounds scary, how am I protected?

The most important question.

The Canton Network is not like most networks where transfers are irrevocable. Instead, when transferring assets on Canton you are really creating a transfer _proposal_.&#x20;

Transfer proposals lock funds so they are ready for transfer, but until the recipient accepts the transfer the tokens stay in your wallet. Our&#x20;

Transfer proposals automatically expire after a configurable period of time (set by your wallet) and the funds returned to your _available_ balance, having never left your wallet. Additionally, if your wallet allows, you may revoke transfer proposals at any time before the recipient has accepted.&#x20;

If you "send" (transfer proposal) tokens to a Magic Address and do not promptly get the expected return, you can revoke the offer (if supported by your wallet) or wait for it to automatically expire.

<details>

<summary>What happens if I make a transfer, but your system is down?</summary>

We take extra precautions via monitoring and redundancy to ensure our systems are always online, however, unexpected events can still occur.

If our system is down or taking too long to execute, and if your wallet supports it, you may revoke the transfer proposals to cancel the operation. Alternatively, wait for the transfer proposal to expire automatically.

</details>

<details>

<summary>I sent an asset/token to the wrong address</summary>

If you send tokens to a Pool Address for a pool that does not use that token, Tradecraft will automatically reject your transfer proposal and the funds should once more appear in your available balance.

</details>

<details>

<summary>Adding Liquidity: I sent one asset before deciding not to pool, how do I get my asset back?</summary>

You may revoke the transfer proposal for the first asset you sent, or wait for it to expire automatically.

</details>

<details>

<summary>Adding Liquidity: What happens if I send the wrong amount of either token?</summary>

If you send too much or too little of either token to the Pool Address, Tradecraft will calculate the remainder of either token and send it back to you, after adding both tokens in the correct ratio to the pool.

You'll need to send both tokens before Tradecraft can calculate any remaining amount to be returned, and pool the rest.

</details>
