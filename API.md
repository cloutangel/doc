# API documentation

URL for requests – <https://api.cloutangel.com>. Your request should be JSON and sent using POST or GET depending on method you invoke. Response comes in JSON format.

## Request format

To make request to API you need to get API token. This token should be passed in header **X-API-Token**.

You can request using 2 methods:

- JSON request and POST
- Normal URL request with GET and parameters

But all responses are in JSON.

## Response format

Each response has JSON format and following structure:

```json
[error, result]
```

Where **error** will be null on success or contain string represenation of error. And **result** contains mixed data of response and depends on requested method. In case of error this field contains extra data about error.

## Methods

### Current block height

Get block height of current chain.

| Param | Value |
|-|-|
| Path | /v1/block/height |
| Method | GET |
| Params | None |
| Result | Block height as integer |

Example:

```bash
curl -H "X-API-Token: $token" -H 'Content-type: application/json'  -X GET https://api.cloutangel.com/v1/block/height
```

Result:

```json
[
  null,
  23371
]
```

### Get block by height or hash

Get block by its height or block hash with all transactions in it.

| Param | Value |
|-|-|
| Path | /v1/block/get/:block |
| Method | GET |
| Params | :block param in url accept numeric block height or block hash. You can pass extra params using JSON and POST or GET with just url. |
| Result | Information about requested block |

Params:
| Param | Type | Info |
|-|-|-|
| expand | bool | in case if you pass true all transactions of block are expanded |
| offset | unsigned int | offset for transactions we select
| limit | unsigned int | how many transactions to select |

Example:

Get by height

```bash
curl -H "X-API-Token: $token" -H 'Content-type: application/json' -X GET https://api.cloutangel.com/v1/block/get/1
```

or by hash

```bash
curl -H "X-API-Token: $token" -H 'Content-type: application/json' -X GET https://api.cloutangel.com/v1/block/get/00000016c72096930787c7e7de4ad069198c2137feddeecbe6a9ec4d61cb6870
```

Result:

```json
[
  null,
  {
    "Header": {
      "BlockHashHex": "00000016c72096930787c7e7de4ad069198c2137feddeecbe6a9ec4d61cb6870",
      "Version": 0,
      "PrevBlockHashHex": "5567c45b7b83b604f9ff5cb5e88dfc9ad7d5a1dd5818dd19e6d02466f47cbd62",
      "TransactionMerkleRootHex": "fd8e08af2ae7f62cdc9676ca3d9d6df9fa801c2263c494503ded47b4164bb88c",
      "TstampSecs": 1614598233,
      "Height": 1,
      "Nonce": 88348
    },
    "Transactions": ["3JuETkEpP92uwnvTDYvhB5uSyAXqG8fDBaUA9UasAijChf3ZAkUPDT"],
    "TransactionCount": 1
  }
]
```

### Get transaction info by base58 check id

Retreive full transaction info by its id.

| Param | Value |
|-|-|
| Path | /v1/tx/get/:base58check |
| Method | GET |
| Params | :base58check is transaction is starting with 3J |
| Result | Information about requested transaction |

Example:

```bash
curl -H "X-API-Token: $token" -H 'Content-type: application/json' -X GET https://api.cloutangel.com/v1/tx/get/3JuETkEpP92uwnvTDYvhB5uSyAXqG8fDBaUA9UasAijChf3ZAkUPDT
```

Result:

```json
[
  null, 
  {
    "TransactionIDBase58Check": "3JuETkEpP92uwnvTDYvhB5uSyAXqG8fDBaUA9UasAijChf3ZAkUPDT",
    "RawTransactionHex": "000102559949a3a9bce770aa526b1006935f3f896645a4622add60f83a256adb557e368094ebdc03010b0a97f68498b39985d0b101000000",
    "Inputs": null,
    "Outputs": [{
      "PublicKeyBase58Check": "BC1YLgKZyfgyNntCMXAZYExM8JooYqrYvVsrR8d8XVxorDruYFdq31p",
      "AmountNanos": 1000000000
    }],
    "SignatureHex": "",
    "TransactionType": "BLOCK_REWARD",
    "BlockHashHex": "00000016c72096930787c7e7de4ad069198c2137feddeecbe6a9ec4d61cb6870",
    "TransactionMetadata": {
      "BlockHashHex": "00000016c72096930787c7e7de4ad069198c2137feddeecbe6a9ec4d61cb6870",
      "TxnIndexInBlock": 0,
      "TxnType": "BLOCK_REWARD",
      "TransactorPublicKeyBase58Check": "8mkU8yaVLs",
      "AffectedPublicKeys": [{
        "PublicKeyBase58Check": "BC1YLgKZyfgyNntCMXAZYExM8JooYqrYvVsrR8d8XVxorDruYFdq31p",
        "Metadata": "BasicTransferOutput"
      }],
      "BasicTransferTxindexMetadata": {
        "TotalInputNanos": 0,
        "TotalOutputNanos": 1000000000,
        "FeeNanos": 0,
        "UtxoOpsDump": ""
      },
      "BitcoinExchangeTxindexMetadata": null,
      "CreatorCoinTxindexMetadata": null,
      "CreatorCoinTransferTxindexMetadata": null,
      "UpdateProfileTxindexMetadata": null,
      "SubmitPostTxindexMetadata": null,
      "LikeTxindexMetadata": null,
      "FollowTxindexMetadata": null,
      "PrivateMessageTxindexMetadata": null,
      "SwapIdentityTxindexMetadata": null
    },
    "TxnIndexInBlock": 0,
    "BlockHeight": 1
}
]
```

### Get address info and transactions belonging to it

Retreive info about public key and transactions that belong to it. All transactions can be expanded. 

**ATTENTION!** Balance is not 100% correct for now but we are working on it to fix. And *token* field is under development.

| Param | Value |
|-|-|
| Path | /v1/address/get/:pubkey |
| Method | GET |
| Params | :pubkey is public key starting with BC1 or username starting with @ |
| Result | Information about requested address and its transactions |

Extra params:

| Param | Type | Value |
|-|-|-|
| expand | bool | Expand transaction info or not. By default its just base 58 check ids |
| offset | unsigned int | Offset for transaction list. Default is 0 |
| limit | unsigned int | How many txs we fetch. Default is 10.

Example:

```bash
curl -H "X-API-Token: $token" -H 'Content-type: application/json' -X GET https://api.cloutangel.com/v1/address/get/BC1YLgKZyfgyNntCMXAZYExM8JooYqrYvVsrR8d8XVxorDruYFdq31p
```

Result:

```json
[
  null, 
  {
    "address": "BC1YLgKZyfgyNntCMXAZYExM8JooYqrYvVsrR8d8XVxorDruYFdq31p",
    "balance": 12912156054148,
    "user": {
      "username": null,
      "avatar": null,
      "reward": 0
    },
    "token": {
      "holders": 0,
      "value": 0,
      "supply": 0
    },
    "tx_count": 27,
    "offset": 0,
    "limit": 10,
    "txs": [
      "3JuETRfRNzcKMaPgxA6rxWKVfKcZgGue99Z8BS2yF9g3X2RUmw7VbN", 
      "3JuETVCsJbhGwewdpFwbu99AwyoPMoCYq5JQpxaxUGUoGCUUkdn1jv", 
      "3JuETLRBCtg3K7SawtiLQMugYhxRk5Lm8yvQLYTDZfBs19eDJRV3VB", 
      "3JuETvRe4u3B9JB6RquxrpUYcexeH2LG6Vk6UiEen3WBEX6q3r2NkE", 
      "3JuETCe3EVa113FU5GNPCgPsia2ifEhJXD2R69g14hPoEb7mZz4cde", 
      "3JuEUKajjizAK5abBVAGUjxUg4rkRUWcwYQUCYbxwdXx1mgPSjomVL", 
      "3JuETHJrLjvWGxMdotYmaNpucQFsBzkW6SNyAw8HZTkRrKTukbGR3F", 
      "3JuESo75RP7Uteoau2GwAERm5vsLXywK5YL9LCpFJhpnuo4rxcK4Lw", 
      "3JuEStZJ5UrRZA86pAmPV2r76iLjBfALAokqZSXgARZEBcsMkwBPG3", 
      "3JuET1ujPBCCG19S8j73Xpzp7mSHvGxjiLauN7jz7qqhnBWQ69Nd8a", 
      "3JuETNbnFUZqe4Ny1K6RkC5K5FRT3HTH4w5BDtQtrTsBFjY9HDSB4t"
    ]
  }
]
```
