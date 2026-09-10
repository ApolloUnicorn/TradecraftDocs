---
description: Fees and incentive programs that keep pools deep and supply matched to demand.
icon: money-bill-simple-wave
---

# Fees and Pricing

Tradecraft is designed to make every Canton-listed asset reliably available to traders, with deep liquidity and the incentive programs needed to keep pools stocked across conditions.

### How Tradecraft markets work

Tradecraft is an automated market maker (AMM) running on Canton. Pools hold inventory in pairs of tokens. Anyone can [trade against a pool](https://app.gitbook.com/s/dF47DwfCAGYAD1QW2BrD/guide-how-to-trade) or [provide liquidity](https://app.gitbook.com/s/dF47DwfCAGYAD1QW2BrD/guide-how-to-provide-liquidity) to a pool. Inventory providers continuously bring supply to the pools where traders need it, keeping every pair stocked across conditions.

Three groups make Tradecraft markets work:

| Participant             | What they do                                                                        | What they earn                                       |
| ----------------------- | ----------------------------------------------------------------------------------- | ---------------------------------------------------- |
| **Liquidity providers** | Deposit pairs of tokens into pools so that trades have inventory to settle against. | A share of swap fees paid by traders in their pool.  |
| **Inventory providers** | Bring assets from other chains to fulfill trading demand on Canton.                 | The sourcing margin on each supply trade, less fees. |
| **Traders**             | Trade for any purpose: wallet swaps, settlement, hedging, rebalancing.              | Deep available liquidity and a small-trade discount. |

### Standard fees

The base swap fee on Tradecraft is usually **0.30%** (30 basis points) of trade size, paid by the trader at the time of the swap. This fee is shared with the liquidity providers in the pool. The fee amount and the liquidity provider/operator fee split can vary by pool.

Trades below a dynamic size threshold pay a reduced swap fee. Tradecraft updates these values continuously based on conditions; see _The small-trade discount_ below for more.

### Liquidity providers

Liquidity providers (LPs) deposit pairs of tokens into Tradecraft pools so that incoming trades have inventory to settle against. LPs earn a share of every swap fee paid in their pool, in proportion to their share of the pool.

The deeper a pool's inventory, the more demand it can absorb without running thin. Tradecraft's incentive programs are designed to encourage stable, long-term liquidity, so that pools deepen over time and traders can rely on them across conditions.

Anyone can become a liquidity provider on Tradecraft. See the liquidity provider guide.

Additional incentive programs may be available for liquidity providers with larger or longer-term commitments. Liquidity providers participating in these programs are expected to maintain consistent participation across the pairs they cover. Contact [partnerships@tradecraft.fi](mailto:partnerships@tradecraft.fi) to discuss eligibility.

**Example**

Consider an LP holding 10% of a pool. If the pool sees $1,000,000 in daily trade volume:

* Swap fees collected by the pool: $1,000,000 x 0.20% = **$2,000/day**
* LP's share (10%): **$200/day**

LP earnings scale linearly with pool volume and with the LP's share of the pool. _Numbers illustrative._

### Inventory providers

Inventory providers bring assets from other chains to fulfill trading demand on Canton. When demand for a token on Tradecraft outpaces the inventory currently available in the pool, an inventory provider sources the token from where it's available (typically another chain, sometimes another Canton venue) and supplies it to the pool. The provider earns the sourcing margin, the gap between where they sourced the asset and what the Tradecraft pool pays for it, less fees.

Inventory providers are net payers of fees and gas to Tradecraft and the network. When an inventory-supply trade qualifies under Tradecraft's program, Tradecraft refunds part (but not all) of the costs the inventory provider incurred. The sourcing margin covers the rest, making the overall transaction profitable enough to sustain continuous inventory supply.

Without inventory providers bringing supply where it's needed, Tradecraft pools would frequently run thin on the side traders want to buy. With inventory providers continuously sourcing tokens for the pools that need them, Tradecraft pools stay supplied across pairs and conditions.

Inventory provision is permissionless. Any party with the means to source assets and supply them to Tradecraft pools can participate. As Tradecraft's gas costs come down through ongoing protocol improvements, the addressable set of profitable inventory strategies widens.

Inventory providers participating in Tradecraft's program are expected to maintain consistent inventory supply across the pairs they cover; qualifying status is reviewed periodically.

Tradecraft offers a qualified onboarding program for new inventory providers and institutions:

* **Working capital.** A loan of up to 1M CC to support initial operating-capital requirements.
* **Onboarding period.** Full access to incentive benefits during the initial deployment period, without immediate volume or activity thresholds.

Contact [partnerships@tradecraft.fi](mailto:partnerships@tradecraft.fi) to discuss eligibility.

**Example**

An inventory provider notices that the TOKEN/USDC pool is paying 1.0015 USDC per TOKEN to bring in supply, while TOKEN can be sourced for 1.0000 USDC elsewhere. The IP brings $1,000 worth of TOKEN to the pool, in a trade size small enough to qualify for the small-trade discount.

Costs paid by the IP:

* Venue fee at the small-trade discounted rate _(illustrative, \~0.02%)_: $0.20
* Gas: $0.10
* **Total costs: $0.30**

Receipts:

* Sourcing margin (15 bps x $1,000): $1.50
* Inventory-provider refund _(separate from the small-trade discount; illustrative, covers most but not all costs)_: $0.24
* **Total receipts: $1.74**

**Net profit on the trade: $1.44.**

The IP paid $0.30 in fees and gas and received $0.24 back through the inventory-provider refund, remaining a net payer of $0.06 in unrefunded costs. The sourcing margin ($1.50) is the bulk of the IP's earnings; the inventory-provider refund makes the activity slightly more profitable than the small-trade discount alone would. _Numbers illustrative._

### Traders

Traders are the participants Tradecraft is ultimately built for: anyone swapping tokens, whether through a wallet, an integrated app, or a trading desk. The combined effect of the participants and programs above is that traders find deep available inventory across all token pairs Tradecraft supports, and that trades below a dynamic size threshold pay reduced fees regardless of who is executing them.

**Example**

A trader executes a $200 swap (below the dynamic small-trade threshold):

* Discounted venue fee _(illustrative, \~0.02%)_: $0.04
* Gas: $0.10
* **Total: $0.14**

A trader executes a $50,000 swap (above the threshold):

* Venue fee at the base rate (0.30%): $150.00
* Gas: $0.10
* **Total: $150.10**

Small trades are essentially free of venue fees; larger trades pay the base rate. _Numbers illustrative._

### The small-trade discount

Tradecraft's design goal is to keep pool inventory matched to trading demand across all pairs. For that to hold continuously, inventory providers need to find it continuously profitable to source tokens where demand outpaces local supply.

That is the role of the small-trade discount. Tradecraft applies a reduced swap fee to trades below a dynamic size threshold. For modest demand-supply gaps, only discounted small-trade supply remains profitable, so the supply work happens through small, distributed trades. When the gap between demand and local supply is larger, supply trades of any size become profitable for any party, restoring the pool to balance.

The size of the threshold is set by data analysis on trailing volume and other conditions, and updated automatically as conditions change.

### Self-dealing safeguards

Tradecraft's incentive system is designed around the following principles to limit extraction of incentives without performing the underlying economic activity:

* **Self-trading should not be profitable.** Rewards distributed on any trade are designed so that the rewards earned are smaller than the costs of executing it, making it uneconomical for a participant to trade against themselves.
* **Incentives should be proportional to fees paid.** No incentive should pay out more than the system collects in fees on the activity it rewards.
* **Tradecraft should not directly compete with inventory providers for the small-trade supply program.** Where Tradecraft sources its own inventory, it does so transparently and outside that program.

These rules are enforced in the protocol.

### A permissionless system

Liquidity provision and inventory provision are some of the most consistent ways to earn from exchange activity, and they are typically reserved for exchange operators or privileged market makers. Tradecraft is designed to make both roles open and permissionless:

* Liquidity providers can deposit to and withdraw from any pool freely.
* Inventory providers can run any inventory-sourcing strategy against any Tradecraft pool.
* The package any external party can run is the same package Tradecraft itself runs. There is no privileged operator version.

As Tradecraft's gas costs come down through ongoing protocol improvements, the set of profitable strategies widens to include participants who don't currently have the capital efficiency to compete.

### Changes and updates

Fee values and threshold parameters change as conditions change. The most recent fee schedule is the one on this page.
