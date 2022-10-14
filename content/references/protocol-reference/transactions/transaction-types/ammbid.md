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

Bid on an Automated Market Maker's (AMM's) auction slot. If this transaction succeeds, you win the bid and can trade against the AMM at a discounted fee until you are outbid or 24 hours have passed. If you are outbid before 24 hours have passed, you are refunded part of the cost of your bid based on how much time remains.

The price of the auction slot is always denominated in that AMM's LP Tokens; the amount paid is returned to the AMM, decreasing the outstanding balance of LP Tokens.

## Example {{currentpage.name}} JSON

```json
{
  "TransactionType": "AMMBid",
  "AMMID": "4B82E4CB90FBE26EA172A498F7A7B03A2A7C285149A9B18731370F2644B96F7A",
  "Account": "rJVUeRqDFNs2xqA7ncVE6ZoAhPUoaJJSQm",
  "MaxSlotPrice": {
    "currency": "D717347ECCF3E2B373546F02AA016D7ABA13C8F7",
    "issuer": "rLcJnZS6M9DyZ23g3GPvtyuYgT5QFwWXaN",
    "value": "100"
  },
  "AuthAccounts": [
    {
      "AuthAccount": {
        "Account": "rMKXGCbJ5d8LbrqthdG46q3f969MVK2Qeg"
      }
    },
    {
      "AuthAccount": {
        "Account": "rBepJuTLFJt3WmtLXYAxSjtBWAeQxVbncv"
      }
    }
  ],
  "Flags": 0,
  "Sequence": 6,
  "Fee": "12",
  "LastLedgerSequence": 402207
}

```

{% include '_snippets/tx-fields-intro.md' %}

| Field          | JSON Type           | [Internal Type][]  | Required? | Description |
|:---------------|:--------------------|:-------------------|:----------|:------------|
| `AMMID`        | String              | Hash256            | Yes       | The [ledger object ID](ledger-object-ids.html) of the AMM instance this bid is for. |
| `MinSlotPrice` | [Currency Amount][] | Amount             | No        | Pay at least this amount for the slot. Setting this value higher makes it harder for others to outbid you. If omitted, pay the minimum necessary to win the bid. |
| `MaxSlotPrice` | [Currency Amount][] | Amount             | No        | Pay at most this amount for the slot. If the cost to win the bid is higher than this amount, the transaction fails. If omitted, pay as much as necessary to win the bid. |
| `AuthAccounts` | Array               | STArray            | No        | A list of up to 4 additional accounts that you allow to trade at the discounted fee. This cannot include the address of the transaction sender. Each of these objects should be an [AuthAccount object](#authaccount-objects). |

You cannot specify both `MinSlotPrice` and `MaxSlotPrice`.

### AuthAccount Objects

Each member of the `AuthAccounts` array must be an object with the following field:

| Field          | JSON Type | [Internal Type][] | Required? | Description |
|:---------------|:----------|:------------------|:----------|:------------|
| `Account`      | String    | AccountID         | Yes       | The address of the account to authorize. |

Like other "inner objects" that can appear in arrays, the JSON representation of each of these objects is wrapped in an object whose only key is the object type, `AuthAccount`.

## Slot Price Calculations

If successful, the transaction automatically outbids the previous slot owner and debits the bid price from the sender's LP Tokens. The price to win the auction decreases over time, divided into 20 intervals of 72 minutes each. If the sender does not have enough LP Tokens to win the bid, or the price of the bid is higher than the transaction's `MaxSlotPrice` value, the transaction fails with a `tecAMM_FAILED_BID` result.

- If the auction slot is currently empty, expired, or in its last interval, the **minimum bid** is **0.001% of the AMM's total LP Tokens balance**.

    **Caution:** This minimum value is a placeholder and may change before the AMM feature becomes finalized.

- Otherwise, the price to outbid the current holder is calculated using the following formula:

        P = B × 1.05 × (1 - t⁶⁰) + M

    - `P` is the price to outbid, in LP Tokens.
    - `B` is the price of the current bid, in LP Tokens.
    - `t` is the fraction of time elapsed in the current 24-hour slot, rounded down to a multiple of 0.05.
    - `M` is the **minimum bid** as defined above.

    There are two special cases for the cost to outbid someone. In the **first interval** after someone wins the bid, the price to outbid them is only 5% more than their bid:

        P = B × 1.05

    In the **last interval** of someone's slot, the cost to outbid someone is only the minimum bid:

        P = M

**Note:** To make sure all servers in the network reach the same results when building a ledger, time measurements are based on the [official close time](ledgers.html#ledger-close-times) of the previous ledger, which is approximate.

## Refunds

In the case where the transaction outbids the previous slot holder, the AMM refunds the previous holder part of the price of the auction slot. The amount refunded is calculated using the following formula:

```text
R = B × (1 - t)
```

- `R` is the amount to refund, in LP Tokens.
- `B` is the price of the previous bid to be refunded, in LP Tokens.
- `t` is the fraction of time elapsed in the current 24-hour slot, rounded down to a multiple of 0.05.


## AMM Cases previous holder
In addition to errors that can occur for all transactions, {{currentpage.name}} transactions can result in the following [transaction result codes](transaction-results.html):

| Error Code              | Description                                  |
|:------------------------|:---------------------------------------------|
| `tecAMM_FAILED_BID`     | This transaction could not win the auction, either because the sender does not hold enough LP Tokens to pay the necessary bid or because the price to win the auction was higher than the transaction's specified `MaxSlotPrice` value. |
| `tecAMM_INVALID_TOKENS` | The sender of this transaction does not hold enough LP Tokens to meet the slot price. |
| `temBAD_AMM_TOKENS`     | The specified `MinSlotPrice` or `MaxSlotPrice` were not specified as the correct LP Tokens for this AMM. |
| `temBAD_AMM_OPTIONS`    | The transaction specified invalid options, such as a list of `AuthAccounts` that is too long, or specifying both `MinSlotPrice` and `MaxSlotPrice`. |
| `temDISABLED`           | The AMM feature :not_enabled: is not enabled on this network. |
| `terNO_ACCOUNT`         | Either the specified `AMM`'s associated `AccountRoot` object, or one of the other accounts specified in this request, do not exist. |
| | ***TODO: any other errors specific to this transaction type?*** |


<!--{# common link defs #}-->
{% include '_snippets/rippled-api-links.md' %}
{% include '_snippets/tx-type-links.md' %}
{% include '_snippets/rippled_versions.md' %}
