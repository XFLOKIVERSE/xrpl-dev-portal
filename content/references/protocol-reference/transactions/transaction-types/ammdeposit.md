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

| Field         | JSON Type           | [Internal Type][] | Required? | Description |
|:--------------|:--------------------|:------------------|:----------|:------------|
| `AMMID`       | String              | Hash256           | Yes | The AMMID of the AMM instance to deposit into. **Caution:** This is not the ID of the AMM ledger object! |
| `Asset1In`    | [Currency Amount][] | Amount            | No | The amount of one asset to deposit to the AMM. This must match the type of one of the assets (tokens or XRP) that the AMM links. |
| `Asset2In`    | [Currency Amount][] | Amount            | No | The amount of another asset to add to the AMM. If present, this must match the type of the other asset the AMM links and cannot be the same asset as `Asset1In`. |
| `EPrice`      | [Currency Amount][] | Amount            | No | The effective price of the LP Tokens after depositing these funds. Must be omitted if `Asset2In` is provided. ***TODO: need more clarity on how this works*** |
| `LPTokensOut` | [Currency Amount][] | Amount            | No | How many of the AMM's LP Tokens to buy. |


### AMMDeposit Modes

This transaction has several modes, depending on which combination of fields are provided. The modes fall into two categories: 

- **Double-asset deposits**, in which you provide both assets that the AMM links in proportions that match the balance of those assets the AMM already holds. These deposits are not subject to a fee.
- **Single-asset deposits**, in which you provide only one of the AMM's two assets. You can envision a single-asset deposit as trading _some_ of the deposited asset for the other asset, then performing a double-asset deposit. The trading fee applies to the amount you would need to trade for, but not to the rest of the deposit; the amount of the fee is debited from the LP Tokens paid out. _For example, if the AMM's asset pool is split perfectly evenly between USD and EUR, and you try to deposit 100 USD, the amount of LP Tokens you receive is slightly less than if you had deposited 50 EUR + 50 USD, because of the fee for converting some of your USD to an equal amount of EUR._

The following combinations of fields indicate a **double-asset deposit**:

| Fields Specified          | Meaning |
|---------------------------|---------|
| `LPTokensOut` only        | Deposit both of this AMM's assets, in amounts calculated so that you receive the specified amount of LP Tokens in return. The amounts deposited maintain the relative proportions of the two assets the AMM already holds. |
| `Asset1In` and `Asset2In` | Deposit both of this AMM's assets, up to the specified amounts. The actual amounts deposited must maintain the same balance of assets as the AMM already holds, so the amount of either one deposited MAY be less than specified. The amount of LP Tokens you get in return is based on the total value deposited. |

The following combinations of fields indicate a **single asset deposit**:

| Fields Specified             | Meaning |
|------------------------------|---------|
| `Asset1In` only              | Deposit exactly the specified amount of one asset, and receive an amount of LP Tokens based on the resulting share of the pool (minus fees). |
| `Asset1In` and `LPTokensOut` | Deposit up to the specified amount of one asset, so that you receive exactly the specified amount of LP Tokens in return (after fees). |
| `Asset1In` and `EPrice`      | Deposit up to the specified amount of one asset, but pay no more than the specified effective price per LP Token (after fees). |

Any other combination of these fields is invalid.


### Single Asset Deposit Fee Calculations

If you deposit one asset, the AMM charges a fee by reducing the total amount of LP Tokens it pays out, weighted by how much your deposit shifts the balance of assets in the pool. This is equivalent to if you had traded part of the deposit amount for the other asset, then performed a double-asset deposit. The formula for how many LP Tokens you receive for a double-asset deposit is as follows: ***TODO: confirm this***

```
L = T × [(1 + B − F × (1 − W) × B × D) × W − 1]
```

Where:

- `L` is the amount of LP Tokens returned
- `T` is the total outstanding LP Tokens before the deposit ***TODO: or is it after the deposit?***
- `B` is the amount of the asset being deposited
- `F` is the trading fee, as a decimal
- `W` is the weight of the deposit asset in the pool ***TODO: clarify how the weight is calculated***
- `D` is the total amount of the deposit asset in the pool ***TODO: before or after?***


## Error Cases

In addition to errors that can occur for all transactions, {{currentpage.name}} transactions can result in the following [transaction result codes](transaction-results.html):

| Error Code              | Description                                  |
|:------------------------|:---------------------------------------------|
| `temBAD_AMM_OPTIONS`    | The transaction specified an invalid combination of fields. See [AMMDeposit Modes](#ammdeposit-modes). |
| `tecFROZEN`             | The transaction tried to deposit a [frozen](freezes.html) token. |
| `tecAMM_BALANCE`        | The AMM does not have enough of one of the assets to accept the deposit (for example, to satisfy the trade portion of a single-asset deposit) or the sender does not have enough of a given token. |
| `temBAD_AMM_TOKENS`     | The transaction specified the LP Tokens incorrectly; for example, the `issuer` is not the AMM's associated AccountRoot address or the `currency` is not the currency code for this AMM's LP Tokens, or the transaction specified this AMM's LP Tokens in one of the asset fields. |
| `tecAMM_FAILED_DEPOSIT` | The conditions on the deposit could not be satisfied; for example, the requested effective price in the `EPrice` field is too low. |
| `tecAMM_INVALID_TOKENS` | The AMM for this token pair does not exist, one of the calculations resulted in a deposit amount rounding to zero. |
| `tecNO_PERMISSION`      | ***TODO: clarify*** One of the assets to deposit in this transaction uses [Authorized Trust Lines](authorized-trust-lines.html) but ***someone's line??*** is not authorized. |
| `terNO_ACCOUNT`         | The AMM specified in the `AMMID` (or its corresponding AccountRoot object) does not exist. |
<!--{# common link defs #}-->
{% include '_snippets/rippled-api-links.md' %}
{% include '_snippets/tx-type-links.md' %}
{% include '_snippets/rippled_versions.md' %}
