---
html: ammvote.html
parent: transaction-types.html
blurb: Vote on the trading fee for an Automated Market Maker instance.
labels:
  - AMM
status: not_enabled
---
# AMMVote
[[Source]](https://github.com/gregtatcam/rippled/blob/amm-core-functionality/src/ripple/app/tx/impl/AMMVote.cpp "Source")
<!-- TODO: Update source link to merged version when available -->

{% include '_snippets/amm-disclaimer.md' %}

Vote on the trading fee for an Automated Market Maker instance. Up to 8 accounts can vote in proportion to the amount of the AMM's LP Tokens they hold. Each new vote re-calculates the AMM's trading fee based on a weighted average of the votes.

## Example {{currentpage.name}} JSON

```json
{
  "TransactionType": "AMMVote",
  "AMMID": "4B82E4CB90FBE26EA172A498F7A7B03A2A7C285149A9B18731370F2644B96F7A",
  "Account": "rJVUeRqDFNs2xqA7ncVE6ZoAhPUoaJJSQm",
  "TradingFee": 600,
  "Flags": 0,
  "Sequence": 5,
  "Fee": "12",
  "LastLedgerSequence": 369957
}
```

{% include '_snippets/tx-fields-intro.md' %}

| Field        | JSON Type | [Internal Type][] | Required? | Description |
|:-------------|:----------|:------------------|:----------|:------------|
| `AMMID`      | String    | Hash256           | Yes       | The [ledger object ID](ledger-object-ids.html) of the AMM instance to withdraw from. |
| `TradingFee` | Number    | UInt16            | Yes       | The proposed fee to vote for, in units of 1/100,000; a value of 1 is equivalent to 0.001%. The maximum value is 65000, indicating a 65% fee. |

## Error Cases

In addition to errors that can occur for all transactions, {{currentpage.name}} transactions can result in the following [transaction result codes](transaction-results.html):

| Error Code              | Description                                  |
|:------------------------|:---------------------------------------------|
| `tecAMM_INVALID_TOKENS` | The sender cannot vote because they do not hold any of this AMM's LP Tokens. |
| `tecAMM_FAILED_VOTE`    | There are already 8 votes from accounts that hold more LP Tokens than the sender of this transaction. |
| `terNO_ACCOUNT`         | The AMM specified in the `AMMID` (or its corresponding AccountRoot object) does not exist. |
| `temBAD_FEE`            | The `FeeVal` from this transaction is not valid. |


<!--{# common link defs #}-->
{% include '_snippets/rippled-api-links.md' %}
{% include '_snippets/tx-type-links.md' %}
{% include '_snippets/rippled_versions.md' %}
