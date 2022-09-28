---
html: amm.html
parent: ledger-object-types.html
blurb: The definition and details of an Automated Market Maker (AMM) instance.
labels:
  - AMM
status: not_enabled
---
# AMM

{% include '_snippets/amm-disclaimer.md' %}

The `AMM` object type describes a single Automated Market Maker (AMM) instance.


## Example AMM JSON

```json
TODO
```

## AMM Fields

The `AMM` object has the following fields:

| Field            | JSON Type           | [Internal Type][] | Description  |
|:-----------------|:--------------------|:------------------|:-------------|
| `AMMToken`       | Object              | STObject          | Defines the two assets this AMM trades, as an [AMMToken Object](#ammtoken-object). |
| `AccountRootID`  | String              | AccountID         | The address of the special account that holds this AMM's assets. |
| `AuctionSlot`    | Object              | STObject          | Details of the current owner of the auction slot, as an [Auction Slot object](#auction-slot-object). |
| `LPTokenBalance` | [Currency Amount][] | Amount            | The total outstanding balance of liquidity provider tokens from this AMM instance. The holders of these tokens can vote on the AMM's trading fee in proportion to their holdings, or redeem the tokens for a share of the AMM's assets. |
| `TradingFee`     | Number              | UInt16            | The percentage fee to be charged for trades against this AMM instance, in units of 1/10,000. The maximum value is 65000, for a 65% fee. |
| `VoteSlots`      | Array               | STArray           | A list of vote objects, representing votes on the pool's trading fee. |

### AMMToken Object

The `AMMToken` field is an object that defines the assets

### Auction Slot Object

The `AuctionSlot` field contains an object with the following nested fields:

| Field           | JSON Type                 | [Internal Type][]    | Required? | Description |
|:----------------|:--------------------------|:---------------------|:----------|:--|
| `Account`       | String - Address          | AccountID            | Yes       | The current owner of this auction slot. |
| `AuthAccounts`  | Array of String - Address | STArray of AccountID | No        | A list of at most 4 additional accounts that are authorized to trade at the discounted fee for this AMM instance. |
| `DiscountedFee` | String                    | UInt32               | Yes       | The trading fee to be charged to the auction owner, in the same format as `TradingFee`. By default this is 0, meaning that the auction owner can trade at no fee instead of the standard fee for this AMM. |
| `Price`         | String - Number           | Amount               | Yes       | The number of `LPTokens` the auction owner paid to win this slot. ***TODO: normally Amount types would be serialized as an object rather than just a string value. Confirm.*** |
| `TimeStamp`     | String                    | UInt32               | Yes       | The time when this slot was bought, in [seconds since the Ripple Epoch][]. |

## AMM Flags

There are currently no flags defined for `AMM` objects.

## AMM ID Format

The ID of an `AMM` object is the [SHA-512Half][] of the following values, concatenated in order:

1. The `AMM` space key (`0x0041`)
0. The AccountID of the first asset's issuer.
0. The 160-bit currency code of the first token.
0. The AccountID of the second asset's issuer.
0. The 160-bit currency code of the second token.

For XRP, use all 0's for both the token and the issuer.

<!--{# common link defs #}-->
{% include '_snippets/rippled-api-links.md' %}			
{% include '_snippets/tx-type-links.md' %}			
{% include '_snippets/rippled_versions.md' %}
