---
html: ammwithdraw.html
parent: transaction-types.html
blurb: Return LPTokens into an Automated Market Maker in exchange for a share of the assets the pool holds.
labels:
  - AMM
status: not_enabled
---
# AMMWithdraw
[[Source]](https://github.com/gregtatcam/rippled/blob/amm-core-functionality/src/ripple/app/tx/impl/AMMWithdraw.cpp "Source")
<!-- TODO: Update source link to merged version when available -->

{% include '_snippets/amm-disclaimer.md' %}

Withdraw funds an Automated Market-Maker (AMM) instance by returning the AMM's liquidity provider tokens (`LPTokens`).

## Example {{currentpage.name}} JSON

```json
TODO
```

{% include '_snippets/tx-fields-intro.md' %}
<!--{# fix md highlighting_ #}-->

| Field      | JSON Type           | [Internal Type][] | Required? | Description |
|:-----------|:--------------------|:------------------|:----------|:------------|
| `AMMID`    | String              | Hash256           | Yes | The [ledger object ID](ledger-object-id.html) of the AMM instance to withdraw from. |
| `Asset1Out` | [Currency Amount][] | Amount            | No | The amount of one asset to withdraw from the AMM. This must match the type of one of the assets (tokens or XRP) that the AMM trades. |
| `Asset2Out` | [Currency Amount][] | Amount            | No | The amount of another asset to withdraw from the AMM. If present, this must match the type of the other asset the AMM trades and cannot be the same type as `Asset1Out`. |
| `EPrice`   | [Currency Amount][] | Amount            | No | The effective price of the `LPTokens` after withdrawing these funds. ***TODO: need more clarity on how this works*** |
| `LPTokens` | [Currency Amount][] | Amount            | No | How many of the AMM's `LPTokens` to redeem. |


### AMMDeposit Modes

This transaction has several modes, depending on which combination of fields are provided. The valid modes are:

- `LPTokens` only, or enable the `tfAMMWithdrawAll` flag
- `Asset1Out` only
- `Asset1Out` and `Asset2Out`
- `Asset1Out` and `LPTokens`; or `Asset1Out` and enable the `tfAMMWithdrawAll` flag
- `Asset1Out` and `EPrice`

Any other combination of these fields is invalid.

***TODO: clarify the intended purpose and semantics of all 5 modes.***


### AMMDeposit Flags

***TODO: `tfAMMWithdrawAll`, possibly others?***

## Error Cases

In addition to errors that can occur for all transactions, {{currentpage.name}} transactions can result in the following [transaction result codes](transaction-results.html):

| Error Code           | Description                                  |
|:---------------------|:---------------------------------------------|
| `temBAD_AMM_OPTIONS` | The transaction specified an invalid combination of fields. See [AMMDeposit Modes](#ammdeposit-modes). |
| `tecFROZEN`          | The transaction tried to deposit a [frozen](freezes.html) token. |
| `tecAMM_BALANCE`     | ***TODO: clarify "reserves or tokens balance is zero"*** |
| `temBAD_AMM_TOKENS`  | ***TODO: clarify "invalid LPTokens"*** |
| `tecAMM_FAILED_DEPOSIT` | ***TODO: clarify*** |
| `tecAMM_INVALID_TOKENS` | ***TODO: clarify*** |
| ***TODO: possibly other codes*** | |

<!--{# common link defs #}-->
{% include '_snippets/rippled-api-links.md' %}
{% include '_snippets/tx-type-links.md' %}
{% include '_snippets/rippled_versions.md' %}
