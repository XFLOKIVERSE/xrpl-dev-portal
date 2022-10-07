---
html: ammbid.html
parent: transaction-types.html
blurb: Bid on an Automated Market Maker's auction slot, which grants a discounted fee.
labels:
  - AMM
status: not_enabled
---
# AMMBid
[[Source]](https://github.com/gregtatcam/rippled/blob/amm-core-functionality/src/ripple/app/tx/impl/AMMBid.cpp "Source")
<!-- TODO: Update source link to merged version when available -->

{% include '_snippets/amm-disclaimer.md' %}

Bid on an Automated Market Maker's (AMM's) auction slot. At any given time, the current auction winner can trade against the AMM at a discounted fee.

The price of the auction slot is always denominated in that AMM's LP Tokens; the amount paid is returned to the AMM, decreasing the outstanding balance of LP Tokens.

## Example {{currentpage.name}} JSON

```json
TODO
```

{% include '_snippets/tx-fields-intro.md' %}
<!--{# fix md highlighting_ #}-->

| Field          | JSON Type           | [Internal Type][]  | Required? | Description |
|:---------------|:--------------------|:-------------------|:----------|:------------|
| `AMMID`        | String              | Hash256            | Yes       | The [ledger object ID](ledger-object-id.html) of the AMM instance this bid is for. |
| `MinSlotPrice` | [Currency Amount][] | Amount             | No        | The minimum price to pay for the slot. If omitted, pay the [default slot price](#default-slot-price). ***TODO: clarify minimum price*** |
| `MaxSlotPrice` | [Currency Amount][] | Amount             | No        | The maximum price to pay for the slot. |
| `AuthAccounts` | Array               | Array of AccountID | No        | A list of up to 4 additional accounts that are authorized to trade at the discounted fee if this bid wins the auction. This cannot include the address of the transaction sender or the AMM's associated special account. |


## Default Slot Price

The price of the auction slot decays exponentially over time. 


## Error Cases

In addition to errors that can occur for all transactions, {{currentpage.name}} transactions can result in the following [transaction result codes](transaction-results.html):

| Error Code           | Description                                  |
|:---------------------|:---------------------------------------------|
| `temBAD_AMM_TOKENS`  | The specified `MinSlotPrice` or `MaxSlotPrice` were not specified as the correct LP Tokens for this AMM. |
| `terNO_ACCOUNT`      | Either the specified `AMM`'s associated `AccountRoot` object, or one of the other accounts specified in this request, do not exist. |
| `temBAD_AMM_OPTIONS` | The transaction specified invalid options, such as a list of `AuthAccounts` that is too long. |
| `tecAMM_INVALID_TOKENS` | The sender of this transaction does not hold enough LP Tokens to meet the slot price. |
| | ***TODO: any other errors specific to this transaction type?*** |


<!--{# common link defs #}-->
{% include '_snippets/rippled-api-links.md' %}
{% include '_snippets/tx-type-links.md' %}
{% include '_snippets/rippled_versions.md' %}
