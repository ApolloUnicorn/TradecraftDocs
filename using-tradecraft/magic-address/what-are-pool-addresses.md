---
icon: paste
layout:
  width: default
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
  tags:
    visible: true
  actions:
    visible: true
metaLinks:
  alternates:
    - /broken/spaces/yE16Xb3IemPxJWydtPOj/pages/QPzbTvC6XsT5gERiU43E
---

# What are Pool Addresses?

Pool Addresses provide a way to interact with the Tradecraft exchange from your wallet without running a Validator Node and without browser extension wallets that are able to connect to web applications (as none yet exist for the Canton ecosystem).

For each Tradecraft pool we've set up Party IDs that monitor incoming transfer offers and perform specific actions when received. Each action results in a transfer offer back to the user – a swapped asset (trade), LP tokens (add liquidity), or both assets in a pool (remove liquidity).

With Canton Network's transfer proposal feature, assets are safe in your wallet until Tradecraft atomically swaps them, guaranteeing a successful transaction with returned output.

This allows Tradecraft to be used from any wallet that supports CIP-56 tokens (see [wallet-support.md](wallet-support.md "mention")).
