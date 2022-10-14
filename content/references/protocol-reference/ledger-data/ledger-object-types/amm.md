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
{
  "AMMAccount": "rLcJnZS6M9DyZ23g3GPvtyuYgT5QFwWXaN",
  "AMMToken": {
    "Token1": {
      "TokenCurrency": "0000000000000000000000000000000000000000",
      "TokenIssuer": "0000000000000000000000000000000000000000"
    },
    "Token2": {
      "TokenCurrency": "0000000000000000000000005453540000000000",
      "TokenIssuer": "F2F97C4301C80D60F86653A319AA7F302C70B83B"
    }
  },
  "AuctionSlot": {
    "Account": "rJVUeRqDFNs2xqA7ncVE6ZoAhPUoaJJSQm",
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
    "DiscountedFee": 0,
    "Price": {
      "currency": "D717347ECCF3E2B373546F02AA016D7ABA13C8F7",
      "issuer": "rLcJnZS6M9DyZ23g3GPvtyuYgT5QFwWXaN",
      "value": "0.7115124735378855"
    },
    "TimeStamp": 719029222
  },
  "Flags": 0,
  "LPTokenBalance": {
    "currency": "D717347ECCF3E2B373546F02AA016D7ABA13C8F7",
    "issuer": "rLcJnZS6M9DyZ23g3GPvtyuYgT5QFwWXaN",
    "value": "71150.53584131501"
  },
  "LedgerEntryType": "AMM",
  "TradingFee": 601,
  "VoteSlots": [
    {
      "VoteEntry": {
        "Account": "rJVUeRqDFNs2xqA7ncVE6ZoAhPUoaJJSQm",
        "FeeVal": 600,
        "VoteWeight": 100000
      }
    }
  ],
  "index": "4C2D6EBC856DE96E1F4859B140CDE495FD878A86E165712F46FF4F2C1D6F16C1"
}
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

The `AMMToken` field is an object that defines the assets this AMM trades. Each asset can be XRP or a [fungible token](tokens.html) but they cannot both be XRP.

| Field            | JSON Type           | [Internal Type][] | Description  |
|:-----------------|:--------------------|:------------------|:-------------|
| `Token1` | Object | STObject | The first asset this AMM trades, as an asset definition object. |
| `Token2` | Object | STObject | The second asset this AMM trades. |

Both `Token1` and `Token2` objects have the following sub-fields:

| Field            | JSON Type           | [Internal Type][] | Description  |
|:-----------------|:--------------------|:------------------|:-------------|
| `TokenCurrency` | String | Currency Code | The currency code of this asset. As a special case, all 0's refers to XRP. In JSON, this is always hexadecimal even when it uses the "standard currency code" format. |
| `TokenIssuer` | String | AccountID | The issuer for this asset. As a special case, all 0's refers to XRP. In JSON, this is always hexadecimal, not base58. |

### Auction Slot Object

The `AuctionSlot` field contains an object with the following nested fields:

| Field           | JSON Type           | [Internal Type][] | Required? | Description |
|:----------------|:--------------------|:------------------|:----------|:--|
| `Account`       | String - Address    | AccountID         | Yes       | The current owner of this auction slot. |
| `AuthAccounts`  | Array               | STArray           | No        | A list of at most 4 additional accounts that are authorized to trade at the discounted fee for this AMM instance. |
| `DiscountedFee` | String              | UInt32            | Yes       | The trading fee to be charged to the auction owner, in the same format as `TradingFee`. By default this is 0, meaning that the auction owner can trade at no fee instead of the standard fee for this AMM. |
| `Price`         | [Currency Amount][] | Amount            | Yes       | The amount the auction owner paid to win this slot, in LP Tokens. |
| `TimeStamp`     | String              | UInt32            | Yes       | The time when this slot was bought, in [seconds since the Ripple Epoch][]. |

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
