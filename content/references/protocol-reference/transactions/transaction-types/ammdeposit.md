---
html: ammdeposit.html
parent: transaction-types.html
blurb: Deposit funds into an Automated Market Maker in exchange for LPTokens.
labels:
  - AMM
status: not_enabled
---
# AMMDeposit
[[Source]](https://github.com/gregtatcam/rippled/blob/amm-core-functionality/src/ripple/app/tx/impl/AMMDeposit.cpp "Source")
<!-- TODO: Update source link to merged version when available -->

{% include '_snippets/amm-disclaimer.md' %}

Deposit funds into an Automated Market-Maker (AMM) instance and receive the AMM's liquidity provider tokens (_LP Tokens_) in exchange. You can deposit one or both of the assets that the AMM instance trades.

## Example {{currentpage.name}} JSON

```json
{
  "TransactionType": "AMMDeposit",
  "AMMID": "4B82E4CB90FBE26EA172A498F7A7B03A2A7C285149A9B18731370F2644B96F7A",
  "Account": "rJVUeRqDFNs2xqA7ncVE6ZoAhPUoaJJSQm",
  "Asset1In": {
    "currency": "TST",
    "issuer": "rP9jPyP5kyvFRb6ZiRghAGw5u8SGAmU4bd",
    "value": "2.5"
  },
  "Asset2In": "30000000",
  "Flags": 0,
  "Sequence": 4,
  "Fee": "12",
  "LastLedgerSequence": 369823
}
```

{% include '_snippets/tx-fields-intro.md' %}

| Field      | JSON Type           | [Internal Type][] | Required? | Description |
|:-----------|:--------------------|:------------------|:----------|:------------|
| `AMMID`    | String              | Hash256           | Yes | The [ledger object ID](ledger-object-ids.html) of the AMM instance to deposit into. |
| `Asset1In` | [Currency Amount][] | Amount            | No | The amount of one asset to deposit to the AMM. This must match the type of one of the assets (tokens or XRP) that the AMM trades. |
| `Asset2In` | [Currency Amount][] | Amount            | No | The amount of another asset to add to the AMM. If present, this must match the type of the other asset the AMM trades and cannot be the same asset as `Asset1In`. |
| `EPrice`   | [Currency Amount][] | Amount            | No | The effective price of the LP Tokens after depositing these funds. Must be omitted if `Asset2In` is provided. ***TODO: need more clarity on how this works*** |
| `LPTokens` | [Currency Amount][] | Amount            | No | How many of the AMM's LP Tokens to buy. |


### AMMDeposit Modes

This transaction has several modes, depending on which combination of fields are provided. The valid modes are:

| Fields Specified          | Fee? | Meaning |
|---------------------------|------|---|
| `LPTokens` only           | None | Deposit both of this AMM's assets, in amounts calculated so that you receive the specified amount of LP Tokens in return. The amounts deposited maintain the relative proportions of the two assets the AMM already holds. |
| `Asset1In` only           | ***TODO: "Fee in terms of the specified assset"*** | Deposit a specified amount of one of this AMM's assets, and receive an amount of LP Tokens based on ***TODO:???*** |
| `Asset1In` and `Asset2In` | None | Deposit both of this AMM's assets, up to the specified amounts. The actual amounts deposited must maintain the same balance of assets as the AMM already holds, so the amount of either one debited MAY be less than specified. The amount of LP Tokens you get in return is based on the total value deposited. |
| `Asset1In` and `LPTokens` | ***TODO: "Fee in terms of the specified assset"*** | Deposit one of this AMM's assets, up to the specified amount, so that you receive the specified amount of LP Tokens in return. |
| `Asset1In` and `EPrice`   | ***TODO: ???*** | Deposit one of this AMM's assets, up to the specified amount, so that you pay no more than the specified effective price per LP Token. |

Any other combination of these fields is invalid.


## Error Cases

In addition to errors that can occur for all transactions, {{currentpage.name}} transactions can result in the following [transaction result codes](transaction-results.html):

| Error Code              | Description                                  |
|:------------------------|:---------------------------------------------|
| `temBAD_AMM_OPTIONS`    | The transaction specified an invalid combination of fields. See [AMMDeposit Modes](#ammdeposit-modes). |
| `tecFROZEN`             | The transaction tried to deposit a [frozen](freezes.html) token. |
| `tecAMM_BALANCE`        | The sender does not have enough of one of the assets to deposit the specified amount. |
| `temBAD_AMM_TOKENS`     | The transaction specified an invalid `LPTokens` field; for example, the `issuer` is not the AMM's associated AccountRoot address or the `currency` is not the currency code for this AMM's LP Tokens. |
| `tecAMM_FAILED_DEPOSIT` | The transaction could not deposit the specified amounts. ***TODO: more explanation for why*** |
| `tecAMM_INVALID_TOKENS` | ***TODO: clarify difference from the temBAD_AMM_TOKENS*** |
| `tecNO_PERMISSION`      | The transaction specified a token that uses [Deposit Authorization](depositauth.html) or [Authorized Trust Lines](authorized-trust-lines.html) but the sender is not authorized. ***TODO: confirm this applies to both DepositAuth & Authorized Trust Lines*** |
| `terNO_ACCOUNT`         | The AMM specified in the `AMMID` (or its corresponding AccountRoot object) does not exist. |
| | ***TODO: possibly other codes*** |

<!--{# common link defs #}-->
{% include '_snippets/rippled-api-links.md' %}
{% include '_snippets/tx-type-links.md' %}
{% include '_snippets/rippled_versions.md' %}
