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

The `AMM` object type describes a single Automated Market Maker instance.


## Example AMM JSON

```json
TODO
```

## AMM Fields

The `AMM` object has the following fields:

| Field                         | JSON Type | [Internal Type][] | Description  |
|:------------------------------|:----------|:------------------|:-------------|
| `AccountRootID` | String | AccountID | The account tied to this AMM instance. |
| `TradingFee` | Number | UInt16 | The percentage fee to be charged for trades against this AMM instance, in units of 1/10,000. The maximum value is 65000, for a 65% fee. |
| `VoteSlots` | Array | ***TODO*** | A list of vote objects ***TODO: for what?*** |
| `AuctionSlot` | Object | ***TODO*** | Details of the current owner of the auction slot, as an [Auction Slot object](#auction-slot-object). |
| `LPTokenBalance` | ***TODO*** | ***TODO*** | The outstanding balance of liquidity provider tokens from this AMM instance. ***TODO: clarify*** |
| `AMMTokens` | ***TODO*** | ***TODO*** | "The tokens of the AMM instance" ***TODO: clarify*** |

### Auction Slot Object

The `AuctionSlot` field contains an object with the following nested fields:

| Field                         | JSON Type | [Internal Type][] | Description  |
|:------------------------------|:----------|:------------------|:-------------|
| `Account` | String - Address | AccountID | The current owner of this auction slot. |
| `TimeStamp` | String | UInt32 | The time when this slot was bought, in [seconds since the Ripple Epoch][]. |
| `DiscountedFee` | String | UInt32 | The trading fee to be charged to the auction owner, in the same format as `TradingFee`. By default this is 0, meaning that the auction owner can trade at no fee instead of the standard fee for this AMM. |
| `Price` | String - Number | Amount | The number of `LPTokens` the auction owner paid to win this slot. ***TODO: normally Amount types would be serialized as an object rather than just a string value. Confirm.*** |
| `AuthAccounts` | Array | ***TODO*** | _(May be omitted)_ A list of at most 4 additional accounts that are authorized to trade at the discounted fee for this AMM instance. |

## AMM Flags

There are currently no flags defined for `AMM` objects.

## AMM ID Format

There are two possible formulas for the `AMM` object's ID, depending on the assets this AMM instance is for trading:

- If the AMM trades **two [fungible token](tokens.html)**, the ID is the [SHA-512Half][] of the following, concatenated in order:
    1. The address of the first token's issuer.
    0. The currency code of the first token.
    0. The address of the second token.
    0. The currency code of the second token.
- If the AMM trades **XRP and one fungible token**, the ID is the [SHA-512Half][] of the following, concatenated in order:
    1. The address of the token's issuer.
    0. The currency code of the token.
    0. The ASCII string `XRP` ***TODO: is it really ASCII, or is it a full 160-bit currency code?***

<!--{# common link defs #}-->
{% include '_snippets/rippled-api-links.md' %}			
{% include '_snippets/tx-type-links.md' %}			
{% include '_snippets/rippled_versions.md' %}
