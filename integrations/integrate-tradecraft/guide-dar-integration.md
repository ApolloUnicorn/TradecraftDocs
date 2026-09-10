---
icon: file-shield
---

# Guide: DAR Integration

## Building on _Tradecraft_.

**INTEGRATION GUIDE - V1.1.19**\
A developer's reference for integrating the Canton-native AMM into your application. The Tradecraft Daml package is available on request. Contact us by email at [info@tradecraft.fi](mailto:info@tradecraft.fi).

***

## 1.0 - Overview

### How Tradecraft works

Tradecraft is a decentralized exchange protocol built natively on the Canton Network in Daml. As an integrator, you will work with a small set of templates and a single shared contract:

**`AMMRules`** : _singleton_\
The protocol contract that exposes every order-creation choice. All choices on it are _nonconsuming_, so the same contract is reused across every interaction. You will need its disclosure once per pool.

**`SwapOrder` - `DepositOrder` - `WithdrawOrder`**\
The three order types. Each is created by exercising a choice on AMMRules and consumes the relevant holdings atomically when it fills.

**`TradingBalance`** : _per user, per instrument_\
Vault-held collateral of a single token, owned by the user. Tokens must be inside a TradingBalance before they can be used as collateral for swap order queueing.

#### The high-level workflow

1. **Discover** available trading pairs.
2. **Submit** a SwapOrder, DepositOrder, or WithdrawOrder.
3. **Monitor** the order until it fills.

## 2.0 - Network Reference

### Endpoints & protocol parties

The following parameters identify the live AMMs on each network. Treat them as configuration that your application should read from a single place rather than hard-coding at call sites.

**API Documentation**

* All Environments - `https://docs.tradecraft.fi/api`

**API base**

* Mainnet - `https://api.tradecraft.fi/v1`
* Testnet - `https://tradecraft.validator.test.canton.obsidian.systems/amm-http-api`
* Devnet - `https://tradecraft.validator.dev.canton.obsidian.systems/amm-http-api`

**Venue**

* Mainnet - `Tradecraft::122096fe076cc065af0cb38f94caa60e8ddfecbe8f0cfe10655ae7aa06fab99c66b7`
* Testnet - `Tradecraft::122087bab51ae50157a06730e296081f8c941d64ec96f9a2e186e159bae25a553d04`
* Devnet - `Tradecraft::122090f9041ae7a635c8471c7d496cf3158c294c154dd5468b19f8d37e949875203e`

**Vault**

* Mainnet - `cs-vault::1220b4cd6098eebafd4c88efd2b3986e86542bdc391060675432cd195ad26bcf013b`
* Testnet - `tradecraft-vault::1220369596a3a24a39f0f383600508971774e4c63e68fcc01313a0707ed70130be8e`
* Devnet - `decentralized-party::1220b1f5f3fe39721e02a886b36a5a57dc215f5d2de275d9823107bcd825b186689d`

## 3.0 - The Integration Workflow

### 3.1 - Discover trading pairs

Fetch the list of every active AMM pool. The response gives you the `ammId` you'll need for every subsequent order.

```shell
$ curl https://api.tradecraft.fi/v1/pools | jq
```

{% hint style="info" %}
**NOTE:** Each pool's `ammId` is the only identifier you need to route an order to it. The venue and vault are the same across every pool on a given network.
{% endhint %}

### 3.2 - Submit orders

All three order types (`SwapOrder`, `DepositOrder`, and `WithdrawOrder`) are created by exercising a nonconsuming choice on the same `AMMRules` singleton. The resulting order contracts are themselves templates you can query for status.

{% hint style="info" %}
**NOTE:** There is now an [API endpoint for token allocation factory](https://docs.tradecraft.fi/api/routes/tokens#get-allocation-factory-token), which may be required for some of the steps described below. See [https://docs.tradecraft.fi/api](https://docs.tradecraft.fi/api) for more information.
{% endhint %}

#### 3.2.1 - Swap orders: _exchanging two tokens via a pool_

Most integrations will spend the majority of their time here. This call allocates the input amount from the actor's wallet holdings and creates the `SwapOrder` atomically. On fill, the input is consumed and the output settles to the actor's wallet.

The `what` field's two constructors describe the two natural shapes of a swap intent:

* **`Long`** - _Buy_ a fixed quantity of the instrument. Pay whatever the AMM quotes.
* **`Short`** - _Sell_ a fixed quantity of the instrument. Receive whatever the AMM quotes.

If you've integrated against Tradecraft's Pool Addresses before, that product is built on `Short`.

{% hint style="danger" %}
#### **Critical safety notice:&#x20;**_**A transfer pre-approval MUST be active for both tokens the user is swapping between BEFORE the order is submitted.**_

Without it, **the swap still occurs** where the input is consumed on fill, but **NO funds are returned to the wallet**. If the order fails or is cancelled, pre-approval is required to receive the input tokens back. In both scenarios, missing pre-approval = **loss of funds**.
{% endhint %}

We highly recommend you test this version of swap order creation on testnet, confirming that tokens are received in the destination wallet before going live.

**Choice Signature**

<pre class="language-daml"><code class="lang-daml"><strong>nonconsuming choice AMMRules_CreateSwapOrderFromHoldingsV2 : AMMRules_CreateSwapOrderFromHoldingsV2_Result
</strong><strong>  with
</strong>    actor : Party
      -- ^ The user performing the swap
    ammId : Text
      -- ^ Identifies which AMM pool this order is for, as returned by /pools
    what : SwapDirection
      -- ^ Direction and amount of swap. Short only - see NOTE below.
    minOut : Optional Decimal
      -- ^ Minimum output amount (slippage protection)
    holdingCids : [ContractId Holding]
      -- ^ The actor's wallet holdings of the input instrument. Holdings in
      --   excess of the swap amount are returned to the actor as change.
    allocationContext : (ContractId AllocationFactory, ExtraArgs)
      -- ^ The input instrument's AllocationFactory and its ExtraArgs, used to
      --   allocate the input amount from the holdings to the vault
</code></pre>

**Result Type**

```daml
data AMMRules_CreateSwapOrderFromHoldingsV2_Result = AMMRules_CreateSwapOrderFromHoldingsV2_Result
  with
    swapOrderCid : ContractId V2.SwapOrder
      -- ^ The pending order (the same queryable SwapOrder template shown above)
    changeCids : [ContractId Holding]
      -- ^ Wallet change : input holdings in excess of the swap amount
```

{% hint style="info" %}
**NOTE:** Only `Short` is currently supported. `Long` orders fail with `"Only short orders are currently supported"`.
{% endhint %}

{% hint style="warning" %}
**Reminder : no pre-approval on the output token = the swap will execute but no funds will be returned.**
{% endhint %}

#### 3.2.2 - Deposit orders: _adding liquidity to a pool_ (\~5.2 kB)

This choice deposits liquidity into a pool and mints LP tokens, into a TradingBalance. Both `amount1` and `amount2` must already exist as funded TradingBalances for the actor.

**Choice Signature**

```daml
nonconsuming choice AMMRules_CreateDepositOrder : ContractId DepositOrder
  with
    actor : Party
    ammId : Text
    amount1 : Decimal
    amount2 : Decimal
    minOut : Optional Decimal
    changeAmounts : Optional [InstrumentAmount]
```

**Resulting Template (Queryable)**

```daml
template DepositOrder
  with
    actor : Party
      -- ^ The party requesting the deposit
    venue : Party
    vault : Party
    ammId : Text
      -- ^ Identifies which AMM pool this deposit is for
    amount1 : Decimal
      -- ^ Amount of instrument1 to deposit (must be pre-funded in TradingBalance)
    amount2 : Decimal
      -- ^ Amount of instrument2 to deposit (must be pre-funded in TradingBalance)
    createdAt : Time
    minOut : Optional Decimal
```

{% hint style="warning" %}
**NOTE:** `amount1` and `amount2` _**must**_ be aligned with the current ratio of the pool. Fetch the current price with `GET /ratio/{tokenA}/{tokenB}`, or let the API compute aligned amounts for you with `GET /quoteLPDeposit/{tokenA}/{tokenB}`.
{% endhint %}

#### 3.2.3 - Withdraw orders: _removing liquidity from a pool_ (\~5.2 kB)

This choice removes liquidity from a pool, and puts the withdrawn tokens into a TradingBalance. The LP tokens must already exist as a funded TradingBalance for the actor.

**Choice Signature**

```daml
nonconsuming choice AMMRules_CreateWithdrawOrder : ContractId WithdrawOrder
  with
    actor : Party
    ammId : Text
    lpTokenAmount : Decimal
    minAmount1 : Optional Decimal
    minAmount2 : Optional Decimal
```

**Resulting Template (Queryable)**

```daml
template WithdrawOrder
  with
    actor : Party
      -- ^ The party requesting the withdrawal
    venue : Party
    vault : Party
    ammId : Text
      -- ^ Identifies which AMM pool this withdrawal is for
    lpTokenAmount : Decimal
      -- ^ Amount of LP tokens to burn (from TradingBalance)
    minAmount1 : Optional Decimal
      -- ^ Minimum amount of instrument1 to receive
    minAmount2 : Optional Decimal
      -- ^ Minimum amount of instrument2 to receive
    createdAt : Time
```

{% hint style="info" %}
**NOTE:** Every order template carries `actor`, `venue`, `vault`, and `ammId` - the same four identity fields. Parameterize these once in your client and reuse across all three constructors.
{% endhint %}

### 3.3 - Monitor for filled orders

When an order is filled by the venue, the original order contract, the consumed input(s), and the new output(s) are produced in the _same_ transaction. Detection is therefore as simple as watching for the order's archival.

**Recommended**\
Use **PQS** (Participant Query Store) to subscribe to the relevant template streams.

**Without PQS**\
Query the participant's contracts endpoint directly for active `SwapOrder` contracts filtered by your actor. When the contract disappears, the fill has occurred; the new `TradingBalance` is created in the same transaction.

### 3.4 - Add TradingBalance (Optional): _providing collateral for swap order queuing_

Before a user can submit queued trades, they must have collateral inside a `TradingBalance` contract. This is a two-call sequence. Fetch the disclosure for the AMMRules contract, then exercise the deposit choice.

#### 3.4.1 - Fetch the AMMRules disclosure

The path segments are the two instruments of the pool you intend to trade against. The example below targets the CBTC/CC pool.

```shell
$ curl https://api.tradecraft.fi/v1/disclosures/CBTC/CC | jq ".amm_rules"
```

#### 3.4.2 - Exercise `AMMRules_AddTradingBalance` (\~10.5 kB)

With the disclosure in hand, exercise the add choice. The user is the actor; their wallet's allocation context provides the tokens.

**Choice Signature**

```daml
nonconsuming choice AMMRules_AddTradingBalance : ContractId TradingBalance
  with
    actor : Party
      -- ^ The user depositing tokens
    allocationContext : (ContractId Allocation, ExtraArgs)
      -- ^ Allocation of tokens the actor is transferring to the vault
    existingBalances : [ContractId TradingBalance]
      -- ^ Existing balances to consolidate with this one
  controller actor
```

{% hint style="info" %}
**TIP:** Avoid UTXO Fragmentation. Pass _**every**_ known `TradingBalance` contract ID for the given asset into `existingBalances` on every call. They will be consolidated atomically into a single new balance, keeping your contract set tidy and reducing downstream gas costs.
{% endhint %}

### 3.5 - Withdraw TradingBalance (Optional): _removing collateral_

When the user is ready to take their tokens back to their wallet, return them using `AMMRules_WithdrawTradingBalance`. Similar to deposits, this is a two-call sequence.

#### 3.5.1 - Fetch the vault holdings

The withdrawal choice needs a list of holding contract IDs from the vault. Request them for the specific token, amount, and recipient.

```shell
$ curl -s -X POST \
    https://api.tradecraft.fi/v1/vault-holdings \
    -H 'Content-Type: application/json' \
    -d '{
      "token": "CC",
      "amount": 1.0,
      "vault": "cs-vault::1220b4cd6098eebafd4c88efd2b3986e86542bdc391060675432cd195ad26bcf013b",
      "receiver": "YourParty::YourNode"
    }' | jq
```

#### 3.5.2 - Exercise `AMMRules_WithdrawTradingBalance` (\~14.2 kB)

The choice supports both full and partial withdrawals, and consolidates fragmented balances in a single transaction.

```daml
nonconsuming choice AMMRules_WithdrawTradingBalance : TransferInstructionResult
  with
    actor : Party
    tradingBalanceCids : [ContractId TradingBalance]
      -- ^ TradingBalances to withdraw from. All will be archived;
      --   they must share the same actor and instrument.
      --   Multi-input is supported : fragmented balances are consolidated.
    amount : Optional Decimal
      -- ^ None = withdraw the full consolidated balance.
      --   Some x = partial withdrawal; remainder stays in a new TradingBalance.
    recipient : Party
      -- ^ Who to send the tokens to
    instrumentTransferInfo : InstrumentTransferInfo
      -- ^ Transfer infrastructure (TransferFactory, expectedAdmin, etc.)
    holdingCids : [ContractId Holding]
      -- ^ The holdings returned by /vault-holdings
```

{% hint style="info" %}
**NOTE:** If the recipient has a transfer pre-approval for the output instrument, the tokens arrive in the recipient's wallet with no further action from you. If the recipient has **no pre-approval**, the tokens will appear as a transfer offer which the recipient must accept.
{% endhint %}

## 4.0 - Type Reference

This is a consolidated list of every choice and template you'll touch as an integrator. Each lives on or is produced by the singleton `AMMRules` contract.

**Choices on `AMMRules`**

* `AMMRules_CreateSwapOrderFromHoldingsV2` → `AMMRules_CreateSwapOrderFromHoldingsV2_Result`
  * contains `ContractId V2.SwapOrder` + `[ContractId Holding]`
* `AMMRules_CreateDepositOrder` → `ContractId DepositOrder`
* `AMMRules_CreateWithdrawOrder` → `ContractId WithdrawOrder`
* `AMMRules_AddTradingBalance` → `ContractId TradingBalance`
* `AMMRules_WithdrawTradingBalance` → `TransferInstructionResult`

**Templates Produced**

* `SwapOrder` : pending swap; archived on fill
* `DepositOrder` : pending LP deposit; archived on fill
* `WithdrawOrder` : pending LP redemption; archived on fill
* `TradingBalance` : per user, per instrument, holds tokens to enable order queueing

**Enums**

* `SwapDirection` : `Long` (buy fixed amount) or `Short` (sell fixed amount)
