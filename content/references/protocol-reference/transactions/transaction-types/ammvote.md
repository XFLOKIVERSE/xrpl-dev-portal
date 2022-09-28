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

Vote on the trading fee for an Automated Market Maker instance.


## Example {{currentpage.name}} JSON

```json
TODO
```

{% include '_snippets/tx-fields-intro.md' %}
<!--{# fix md highlighting_ #}-->

| Field      | JSON Type | [Internal Type][] | Required? | Description |
|:-----------|:----------|:------------------|:----------|:------------|
| `AMMID`    | String    | Hash256           | Yes       | The [ledger object ID](ledger-object-id.html) of the AMM instance to withdraw from. |
| `FeeVal`   | Number    | UInt16            | Yes       | The proposed fee to vote for, in units of 1/100,000; a value of 1 is equivalent to 0.001%. The maximum value is 65000, indicating a 65% fee. |

## Error Cases

In addition to errors that can occur for all transactions, {{currentpage.name}} transactions can result in the following [transaction result codes](transaction-results.html):

| Error Code           | Description                                  |
|:---------------------|:---------------------------------------------|
| ***TODO: errors*** | | 


<!--{# common link defs #}-->
{% include '_snippets/rippled-api-links.md' %}
{% include '_snippets/tx-type-links.md' %}
{% include '_snippets/rippled_versions.md' %}
