---
html: amm_info.html
parent: path-and-order-book-methods.html
blurb: Get info about an Automted Market Maker (AMM) instance.
status: not_enabled
labels:
  - Decentralized Exchange
  - Cross-Currency
  - AMM
---
# amm_info
[[Source]](https://github.com/gregtatcam/rippled/blob/c1e4bfb08bcc9f187d794a71d653003a6148dc68/src/ripple/rpc/handlers/AMMInfo.cpp "Source")
<!-- TODO: Update source link to merged version when available -->

The `{{currentpage.name}}` method gets information about an Automated Market Maker (AMM) instance.

{% include '_snippets/amm-disclaimer.md' %}


### Request Format

An example of the request format:

{% include '_snippets/no-cli-syntax.md' %}

<!-- MULTICODE_BLOCK_START -->

*WebSocket*

```json
{
    "command": "{{currentpage.name}}",
    "asset1": {
      "currency": "XRP"
    },
    "asset2": {
      "currency": "TST",
      "issuer": "rP9jPyP5kyvFRb6ZiRghAGw5u8SGAmU4bd"
    }
}
```

*JSON-RPC*

```json
{
    "method": "{{currentpage.name}}",
    "params": [{
      "asset1": {
        "currency": "XRP"
      },
      "asset2": {
        "currency": "TST",
        "issuer": "rP9jPyP5kyvFRb6ZiRghAGw5u8SGAmU4bd"
      }
    }]
}
```

<!-- MULTICODE_BLOCK_END -->

The request includes the following parameters:

| `Field`  | Type   | Description                        |
|:---------|:-------|:-----------------------------------|
| `asset1` | Object | One of the assets of the AMM to look up, as an object with `currency` and `issuer` fields (omit `issuer` for XRP), like [currency amounts][Currency Amount]. |
| `asset2` | Object | The other of the assets of the AMM, as an object with `currency` and `issuer` fields (omit `issuer` for XRP), like [currency amounts][Currency Amount]. |


### Response Format

An example of a successful response:

<!-- MULTICODE_BLOCK_START -->

*WebSocket*

```json
{
  "result": {
    "AMMAccount": "rUKN5XC4S7SRMB8ezRNADr4WWunJJz8jqK",
    "AMMID": "4B82E4CB90FBE26EA172A498F7A7B03A2A7C285149A9B18731370F2644B96F7A",
    "Asset1": "250000000",
    "Asset2": {
      "currency": "TST",
      "issuer": "rP9jPyP5kyvFRb6ZiRghAGw5u8SGAmU4bd",
      "value": "25"
    },
    "LPToken": {
      "currency": "7C1C520B3141910CEFAFD038F6975C35B45A383A",
      "issuer": "rUKN5XC4S7SRMB8ezRNADr4WWunJJz8jqK",
      "value": "79056.9415042095"
    },
    "TradingFee": 500,
    "ledger_current_index": 226645,
    "validated": false
  },
  "status": "success",
  "type": "response"
}
```

*JSON-RPC*

```json
200 OK

{
  "result": {
    "AMMAccount": "rUKN5XC4S7SRMB8ezRNADr4WWunJJz8jqK",
    "AMMID": "4B82E4CB90FBE26EA172A498F7A7B03A2A7C285149A9B18731370F2644B96F7A",
    "Asset1": "250000000",
    "Asset2": {
      "currency": "TST",
      "issuer": "rP9jPyP5kyvFRb6ZiRghAGw5u8SGAmU4bd",
      "value": "25"
    },
    "LPToken": {
      "currency": "7C1C520B3141910CEFAFD038F6975C35B45A383A",
      "issuer": "rUKN5XC4S7SRMB8ezRNADr4WWunJJz8jqK",
      "value": "79056.9415042095"
    },
    "TradingFee": 500,
    "ledger_current_index": 226620,
    "status": "success",
    "validated": false
  }
}
```

<!-- MULTICODE_BLOCK_END -->

The response follows the [standard format][], with a successful result containing the following fields:

| `Field`                | Type   | Description                                               |
|:-----------------------|:-------|:----------------------------------------------------------|
| (Various)              | (Various) | The fields of the [AMM object](amm.html) for the requested asset pair. (These fields have names starting with uppercase letters.) |
| `ledger_current_index` | Integer | _(Omitted if `ledger_index` is provided instead)_ The [ledger index][] of the current in-progress ledger, which was used when retrieving this information. |
| `ledger_index`         | Integer | _(Omitted if `ledger_current_index` is provided instead)_ The [ledger index][] of the ledger version used when retrieving this information. |
| `validated`            | Boolean | If `true`, the ledger used for this request is validated and these results are final; if omitted or set to `false`, the data is pending and may change. |



### Possible Errors

- Any of the [universal error types][].
- `actNotFound` - The AMM for this asset pair does not exist, or an issuing account specified in the request does not exist.
- `invalidParams` - One or more fields are specified incorrectly, or one or more required fields are missing.

<!--{# common link defs #}-->
{% include '_snippets/rippled-api-links.md' %}
{% include '_snippets/tx-type-links.md' %}
{% include '_snippets/rippled_versions.md' %}
