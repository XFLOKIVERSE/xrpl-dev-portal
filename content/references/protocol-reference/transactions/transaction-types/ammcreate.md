---
html: ammcreate.html
parent: transaction-types.html
blurb: Create a new AMM instance for trading a given currency pair.
labels:
  - AMM
status: not_enabled
---
# AMMCreate
[[Source]](https://github.com/gregtatcam/rippled/blob/amm-core-functionality/src/ripple/app/tx/impl/AMMCreate.cpp "Source")
<!-- TODO: Update source link to merged version when available -->

{% include '_snippets/amm-disclaimer.md' %}

Create a new Automated Market-Maker (AMM) instance for trading a given currency pair.

If successful, this transaction creates both an [AccountRoot object][] and an [AMM object][] which, together, represent the AMM; it also transfers ownership of the starting balance of both assets from the sender to the created `AccountRoot`, and issues an initial balance of liquidity provider tokens (`LPTokens`) from the AMM account to this transaction's sender.

## Example {{currentpage.name}} JSON

```json
{
  "TransactionType": "AMMCreate",
  "Account": "rJVUeRqDFNs2xqA7ncVE6ZoAhPUoaJJSQm",
  "Asset1": {
    "currency": "TST",
    "issuer": "rP9jPyP5kyvFRb6ZiRghAGw5u8SGAmU4bd",
    "value": "25"
  },
  "Asset2": "250000000",
  "TradingFee": 500,
  "Flags": 0,
  "Sequence": 2,
  "Fee": "12",
  "LastLedgerSequence": 142917
}
```

{% include '_snippets/tx-fields-intro.md' %}
<!--{# fix md highlighting_ #}-->

| Field        | JSON Type           | [Internal Type][] | Required? | Description |
|:-------------|:--------------------|:------------------|:----------|:------------|
| `Asset1`     | [Currency Amount][] | Amount            | Yes       | The first of the two assets to fund this AMM with. This must have a positive `value`. |
| `Asset2`     | [Currency Amount][] | Amount            | Yes       | The second of the two assets to fund this AMM with. This must have a positive `value`. |
| `TradingFee` | Number              | UInt16            | Yes       | The fee to charge for trades against this AMM instance, in units of 1/100,000; a value of 1 is equivalent to 0.001%. The maximum value is `65000`, indicating a 65% fee. The minimum value is `0`. |

One or both of `Asset1` and `Asset2` can be [tokens](tokens.html); at most one of them can be [XRP](xrp.html). They cannot both have the same currency code and issuer.

## Error Cases

In addition to errors that can occur for all transactions, {{currentpage.name}} transactions can result in the following [transaction result codes](transaction-results.html):

| Error Code          | Description                                  |
|:--------------------|:---------------------------------------------|
| `temDISABLED`       | The AMM feature :not_enabled: is not enabled on this network. |
| `temINVALID_FLAG`   | The transaction specified an invalid `Flags` value. Since there are currently no flags defined for this transaction type, only [Global Flags](transaction-common-fields.html#global-flags) are allowed. |
| `temBAD_AMM_TOKENS` | The values of `Asset1` and `Asset2` are not valid: for example, both refer to the same token. |
| `temBAD_FEE`        | The `TradingFee` value is invalid. It must be zero or a positive integer and cannot be over 65000. |
| `terNO_ACCOUNT`     | 
| `terNO_AUTH`        | At least one of `Asset1` or `Asset2` requires an [authorized trust line](authorized-trust-lines.html), but the issuer of that token has not authorized the trust line. |
| `terNO_LINE`        | At least one of `Asset1` or `Asset2` requires an [authorized trust line](authorized-trust-lines.html), but no trust line exists. |
| `tecFROZEN`         | At least one of `Asset1` or `Asset2` is currently [frozen](freezes.html). |
| `tecUNFUNDED_AMM`   | The sender does not hold enough money to fund the AMM with the amounts specified in `Asset1` and `Asset2`. |
| `tecAMM_EXISTS`     | There is already another AMM trading this currency pair. |
| ***TODO: possibly other codes*** | |

<!--{# common link defs #}-->
{% include '_snippets/rippled-api-links.md' %}
{% include '_snippets/tx-type-links.md' %}
{% include '_snippets/rippled_versions.md' %}
