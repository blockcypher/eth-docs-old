# Objects

```shell
# All cURL'd Objects are JSON:

{
	key: value,
	key: value,
}
```

Before diving into BlockCypher's endpoints, this section details all the Objects exposed and expected by the API. Some of you might be more interested in the endpoints themselves, in which case, feel free to [skip to the next section.](#blockchain-api) For each Object there's a description and a link to a germane API endpoint.


<aside class="notice">
Objects sometimes contain <b>attributes</b> that are <b><i>optional.</i></b> These are always noted in the <b>description</b>. In these cases, the Object may not be guaranteed to include that <b>attribute</b> in its response.
</aside>

## Blockchain

```shell
curl -s https://api.blockcypher.com/v1/eth/main
{
  "name": "ETH.main",
  "height": 1656653,
  "hash": "c5e3a1df5a295fa536fe2d7f8201f529153753665763e20dd9329659136d0c3c",
  "time": "2016-06-06T21:53:30.368223984Z",
  "latest_url": "https://api.blockcypher.com/v1/eth/main/blocks/c5e3a1df5a295fa536fe2d7f8201f529153753665763e20dd9329659136d0c3c",
  "previous_hash": "781c3b4eb45895cab359a15f6b947c54d6974e9829eaf6937531098107c35e46",
  "previous_url": "https://api.blockcypher.com/v1/eth/main/blocks/781c3b4eb45895cab359a15f6b947c54d6974e9829eaf6937531098107c35e46",
  "peer_count": 200,
  "unconfirmed_count": 11619,
  "high_fee_per_kb": 40000000000,
  "medium_fee_per_kb": 20000000000,
  "low_fee_per_kb": 5000000000,
  "last_fork_height": 1654102,
  "last_fork_hash": "7c592f2594eea525754819ed15345a765db0a63e492bac833cb667296c3dee37"
}
```

A Blockchain represents the current state of the Ethereum blockchain. Typically returned from the [Chain API endpoint](#chain-endpoint).

Attribute | Type | Description
--------- | ---- | -----------
**name** | *string* | The name of the blockchain represented, in the form of $COIN.$CHAIN.
**height** | *integer* | The current height of the blockchain; i.e., the number of blocks in the blockchain.
**hash** | *string* | The hash of the latest confirmed block in the blockchain; in Ethereum, the [hashing function is Keccak/SHA3](https://medium.com/@ConsenSys/are-you-really-using-sha-3-or-old-code-c5df31ad2b0).
**time** | [*time*](https://tools.ietf.org/html/rfc3339) | The time of the latest update to the blockchain; typically when the latest block was added.
**latest_url** | *url* | The BlockCypher URL to query for more information on the latest confirmed block; returns a [Block](#block).
**previous_hash** | *string* | The hash of the second-to-latest confirmed block in the blockchain.
**previous_url** | *url* | The BlockCypher URL to query for more information on the second-to-latest confirmed block; returns a [Block](#block).
**unconfirmed_count** | *integer* | Number of unconfirmed transactions in memory pool (likely to be included in next block).
**high_fee_per_kb** | *integer* | A rolling average of the fee (in wei) paid per kilobyte for transactions to be confirmed within 1 to 2 blocks.
**medium_fee_per_kb** | *integer* | A rolling average of the fee (in wei) paid per kilobyte for transactions to be confirmed within 3 to 6 blocks.
**low_fee_per_kb** | *integer* | A rolling average of the fee (in wei) paid per kilobyte for transactions to be confirmed in 7 or more blocks.
**last_fork_height** | *integer* | ***Optional*** The current height of the latest fork to the blockchain; when no competing blockchain fork present, not returned with endpoints that return Blockchains.
**last_fork_hash** | *string* | ***Optional*** The hash of the latest confirmed block in the latest fork of the blockchain; when no competing blockchain fork present, not returned with endpoints that return Blockchains.

## Block

```shell
curl -s https://api.blockcypher.com/v1/eth/main/blocks/7
{
  "hash": "e0c7c0b46e116b874354dce6f64b8581bd239186b03f30a978e3dc38656f723a",
  "height": 7,
  "chain": "ETH.main",
  "total": 0,
  "fees": 0,
  "size": 1078,
  "ver": 0,
  "time": "2015-07-30T15:28:30Z",
  "received_time": "2015-07-30T15:28:30Z",
  "coinbase_addr": "dd2f1e6e498202e86d8f5442af596580a4f03c2c",
  "relayed_by": "",
  "nonce": 13599487003767981,
  "n_tx": 0,
  "prev_block": "1f1aed8e3694a067496c248e61879cda99b0709a1dfbacd0b693750df06b326e",
  "mrkl_root": "ff8d97d9ca01ee59c793c4da2ba4ce8c31d358b42f216ab2f11af4bb097b6a2b",
  "uncles": [
    "4b8729311c5b59f418c5154fd54d85e6a8b42eabf83a1d3c05c754a8f10354cc"
  ],
  "txids": [],
	"internal_txids": [],
  "depth": 1656646,
  "prev_block_url": "https://api.blockcypher.com/v1/eth/main/blocks/1f1aed8e3694a067496c248e61879cda99b0709a1dfbacd0b693750df06b326e",
  "tx_url": "https://api.blockcypher.com/v1/eth/main/txs/"
}
```

A Block represents the current state of a particular block from a [Blockchain](#blockchain). Typically returned from the [Block Hash](#block-hash-endpoint) and [Block Height](#block-height-endpoint) endpoints.


Attribute | Type | Description
--------- | ---- | -----------
**hash** | *string* | The hash of the block; in Ethereum, the [hashing function is Keccak/SHA3](https://medium.com/@ConsenSys/are-you-really-using-sha-3-or-old-code-c5df31ad2b0)
**height** | *integer* | The height of the block in the blockchain; i.e., there are **height** earlier blocks in its blockchain.
**depth** | *integer* | The depth of the block in the blockchain; i.e., there are **depth** later blocks in its blockchain.
**chain** | *string* | The name of the blockchain represented, in the form of $COIN.$CHAIN
**total** | *integer* | The total number of wei transacted in this block.
**fees** | *integer* | The total number of fees---in wei---collected by miners in this block.
**size** | *integer* | Raw size of block (including header and all transactions) in bytes. 
**ver** | *integer* | Block version. 
**time** | [*time*](https://tools.ietf.org/html/rfc3339) | Recorded time at which block was built.
**received_time** | [*time*](https://tools.ietf.org/html/rfc3339) | The time BlockCypher's servers receive the block.
**coinbase_addr** | *string* | The Ethereum address of the miner that received the coinbase and fee reward.
**relayed_by** | *string* | Address of the peer that sent BlockCypher's servers this block.
**nonce** | *integer* | The number used by a miner to generate this block.
**n_tx** | *integer* | Number of transactions in this block.
**prev_block** | *string* | The hash of the previous block in the blockchain.
**prev_block_url** | *url* | The BlockCypher URL to query for more information on the previous block.
**tx_url** | *url* | The base BlockCypher URL to receive transaction details. To get more details about specific transactions, you must concatenate this URL with the desired transaction hash(es).
**mrkl_root** | *string* | The Merkle root of this block.
**txids** | *array[string]* | An array of transaction hashes in this block (initiated by externally controlled accounts). By default, only 20 are included.
**internal_txids** | *array[string]* | An array of internal transaction hashes (initiated by internal contracts) in this block. By default, only 20 are included.
**next_txids** | *url* | ***Optional*** If there are more transactions that couldn't fit in the **txids** array, this is the BlockCypher URL to query the next set of transactions (within a Block object).
**next_internal_txids** | *url* | ***Optional*** If there are more internal transactions that couldn't fit in the **internal_txids** array, this is the BlockCypher URL to query the next set of transactions (within a Block object).
**uncles** | *array[string]* | List of uncle blocks by hash included by the miner of this block. You can read [more about uncles here.](https://www.cryptocompare.com/mining/guides/what-are-mining-rewards-in-ethereum/)

## TX

```shell
curl -s https://api.blockcypher.com/v1/eth/main/txs/8f39fb4940c084460da00a876a521ef2ba84ad6ea8d2f5628c9f1f8aeb395342
{
  "block_hash": "b35265965f6f14c5d692b2030922fce53067fc0e9d8c8490c31d4ecd3e46500c",
  "block_height": 1564040,
  "block_index": 0,
  "hash": "8f39fb4940c084460da00a876a521ef2ba84ad6ea8d2f5628c9f1f8aeb395342",
  "addresses": [
    "738d145faabb1e00cf5a017588a9c0f998318012",
    "4e9d8b4f18d984f6f0c88d07e4b39201e825cd17"
  ],
  "total": 10153153359437326,
  "fees": 1595580000000000,
  "size": 116,
  "relayed_by": "",
  "confirmed": "2016-05-22T12:43:00Z",
  "received": "2016-05-22T12:43:00Z",
  "ver": 0,
  "double_spend": false,
  "vin_sz": 1,
  "vout_sz": 1,
  "confirmations": 92614,
  "confidence": 1,
  "inputs": [
    {
      "sequence": 273,
      "addresses": [
        "738d145faabb1e00cf5a017588a9c0f998318012"
      ]
    }
  ],
  "outputs": [
    {
      "value": 10153153359437326,
      "script": "4e71d92d",
      "addresses": [
        "4e9d8b4f18d984f6f0c88d07e4b39201e825cd17"
      ]
    }
  ]
}
```

A TX represents the current state of a particular transaction from either a [Block](#block) within a [Blockchain](#blockchain), or an unconfirmed transaction that has yet to be included in a [Block](#block).

Attribute | Type | Description
--------- | ---- | -----------
**block_height** | *integer* | Height of the block that contains this transaction. If this is an unconfirmed transaction, it will equal -1.
**hash** | *string* | The hash of the transaction. 
**addresses** | *array[string]* | Array of Ethereum addresses involved in the transaction.
**total** | *integer* | The total number of wei exchanged in this transaction.
**fees** | *integer* | The total number of fees---in wei---collected by miners in this transaction.
**size** | *integer* | The size of the transaction in bytes.
**relayed_by** | *string* | Address of the peer that sent BlockCypher's servers this transaction. May be empty.
**received** | [*time*](https://tools.ietf.org/html/rfc3339) | Time this transaction was received by BlockCypher's servers.
**ver** | *integer* | Version number of this transaction.
**double_spend** | *bool* | *true* if this is an attempted double spend; *false* otherwise.
**vin_sz** | *integer* | Total number of inputs in the transaction.
**vout_sz** | *integer* | Total number of outputs in the transaction.
**confirmations** | *integer* | Number of subsequent blocks, including the block the transaction is in. Unconfirmed transactions have 0 confirmations.
**inputs** | *Object* | An array object containing a single input with a **sequence** number (used as a nonce for account balances) and an Ethereum account **address**. Only contains one input in the array; we still use an array to maintain parity with the Bitcoin API.
**outputs** | *Object* | An array object containing a single output with **value** (in wei), **script**, and an Ethereum account **address**. Only contains one output in the array; we still use an array to maintain parity with the Bitcoin API.
**confirmed** | [*time*](https://tools.ietf.org/html/rfc3339) | ***Optional*** Time at which transaction was included in a block; only present for confirmed transactions.
**receive_count** | *integer* | ***Optional*** Number of peers that have sent this transaction to BlockCypher; only present for unconfirmed transactions.
**block_hash** | *string* | ***Optional***  Hash of the block that contains this transaction; only present for confirmed transactions.
**double_of** | *string* | ***Optional*** If this transaction is a double-spend (i.e. **double_spend** == *true*) then this is the hash of the transaction it's double-spending.

## TXRef

```shell
{
	"tx_hash": "ba44f568dc77d33893261c041994eeaef17d108a1845ccb9e376afea2acdd0e9",
	"block_height": 1648312,
	"tx_input_n": 0,
	"tx_output_n": -1,
	"value": 50420000000000000,
	"ref_balance": 2845236193385545395,
	"confirmations": 8342,
	"confirmed": "2016-06-05T12:47:08Z",
	"double_spend": false
}
```

A TXRef object represents summarized data about a transaction input or output. Typically found in an array within an [Address](#address) object, which is usually returned from the standard [Address Endpoint](#address-endpoint).

Attribute | Type | Description
--------- | ---- | -----------
**block_height** | *integer* | Height of the block that contains this transaction input/output. If it's unconfirmed, this will equal -1.
**tx_hash** | *string* | The hash of the transaction containing this input/output. 
**tx_input_n** | *integer* | Index of this input in the enclosing transaction. 0 if it's an input, -1 if it's an output.
**tx_output_n** | *integer* |	Index of this output in the enclosing transaction. -1 if it's an input, 0 if it's an output.
**value** | *integer* | The value transfered by this input/output in wei exchanged in the enclosing transaction.
**double_spend** | *bool* | *true* if this is an attempted double spend; *false* otherwise.
**confirmations** | *integer* | Number of subsequent blocks, including the block the transaction is in. Unconfirmed transactions have 0 confirmations.
**ref_balance** | *integer* | ***Optional*** The past balance of the parent address the moment this transaction was confirmed. Not present for unconfirmed transactions.
**confirmed** | [*time*](https://tools.ietf.org/html/rfc3339) | ***Optional*** Time at which transaction was included in a block; only present for confirmed transactions.
**double_of** | *string* | ***Optional*** If this transaction is a double-spend (i.e. **double_spend** == *true*) then this is the hash of the transaction it's double-spending.

## TXSkeleton

```shell
curl -sd '{"inputs":[{"addresses": ["1cc60c94254c0f0f0b957fb7c3dfed2d00e49a4c"]}],"outputs":[{"addresses": ["add42af7dd58b27e1e6ca5c4fdc01214b52d382f"], "value": 4200000000000000}]}' https://api.blockcypher.com/v1/eth/main/txs/new?token=YOURTOKEN
{
  "tx": {
    "block_height": -1,
    "block_index": 0,
    "hash": "5f32efeee4c1132df5aea5ac42735e0e21df995dd1cb0a4196eff4afc81ccb77",
    "addresses": [
      "1cc60c94254c0f0f0b957fb7c3dfed2d00e49a4c",
      "add42af7dd58b27e1e6ca5c4fdc01214b52d382f"
    ],
    "total": 4200000000000000,
    "fees": 861000000000000,
    "size": 44,
    "relayed_by": "",
    "received": "2016-06-07T06:41:25.396799578Z",
    "ver": 0,
    "double_spend": false,
    "vin_sz": 1,
    "vout_sz": 1,
    "inputs": [
      {
        "sequence": 2,
        "addresses": [
          "1cc60c94254c0f0f0b957fb7c3dfed2d00e49a4c"
        ]
      }
    ],
    "outputs": [
      {
        "value": 4200000000000000,
        "addresses": [
          "add42af7dd58b27e1e6ca5c4fdc01214b52d382f"
        ]
      }
    ]
  },
  "tosign": [
    "dd3c2cc1cf735ce5a3bbb5de7fb8f3ac2d9b6e6ec211ec6d8c03f098403e7736"
  ],
	"signatures": [],
	"errors": []
}
```

A TXSkeleton is a convenience/wrapper Object that's used primarily when [Creating Transactions](#creating-transactions) through the [New](#new-transaction-endpoint) and [Send](#send-transaction-endpoint) endpoints.

Attribute | Type | Description
--------- | ---- | -----------
**tx** | *[TX](#tx)* | A temporary [TX](#tx), usually returned fully filled.
**tosign** | *array[string]* | Array of hex-encoded data for you to sign, containing one element for you to sign. Still an array to maintain parity with the Bitcoin API.
**signatures** | *array[string]* | Array of signatures corresponding to all the data in **tosign**, typically provided by you. Only one signature is required.
**errors** | *array["error":string]* | ***Optional*** Array of errors in the form *"error":"description-of-error"*. This is only returned if there was an error in any stage of transaction generation, and is usually accompanied by a HTTP 400 code.

## Address

```shell
curl -s https://api.blockcypher.com/v1/eth/main/addrs/738d145faabb1e00cf5a017588a9c0f998318012
{
  "address": "738d145faabb1e00cf5a017588a9c0f998318012",
  "total_received": 9762206505909057760,
  "total_sent": 6916970312523512365,
  "balance": 2845236193385545395,
  "unconfirmed_balance": 0,
  "final_balance": 2845236193385545395,
  "n_tx": 702,
  "unconfirmed_n_tx": 0,
  "final_n_tx": 702,
  "txrefs": [
    {
      "tx_hash": "ba44f568dc77d33893261c041994eeaef17d108a1845ccb9e376afea2acdd0e9",
      "block_height": 1648312,
      "tx_input_n": 0,
      "tx_output_n": -1,
      "value": 50420000000000000,
      "ref_balance": 2845236193385545395,
      "confirmations": 8342,
      "confirmed": "2016-06-05T12:47:08Z",
      "double_spend": false
    },
    {
      "tx_hash": "99ec589ab8cb9498093c4d0c534bdcfd8bd04bfead1d869e3a79165a4f98af0d",
      "block_height": 1628712,
      "tx_input_n": 0,
      "tx_output_n": -1,
      "value": 10623553359437326,
      "ref_balance": 2895656193385545395,
      "confirmations": 27942,
      "confirmed": "2016-06-02T06:53:55Z",
      "double_spend": false
    },
    {
      "tx_hash": "20551685c6c311738da9f42019eb66fccd2cec4cc48e00d98967365d6ad77164",
      "block_height": 1628222,
      "tx_input_n": 0,
      "tx_output_n": -1,
      "value": 10623553359437326,
      "ref_balance": 2906279746744982721,
      "confirmations": 28432,
      "confirmed": "2016-06-02T05:02:50Z",
      "double_spend": false
    },
		...
  ],
  "hasMore": true,
  "tx_url": "https://api.blockcypher.com/v1/eth/main/txs/"
}
```

An Address represents the state of an Ethereum address/account, containing information about the state of balances and transactions related to this address. Typically returned from the [Address Balance](#address-balance-endpoint), [Address](#address-endpoint), and [Address Detail Endpoint](#address-detail-endpoint).

Attribute | Type | Description
--------- | ---- | -----------
**address** | *string* | The requested address.
**total_received** | *integer* |Total amount of confirmed wei received by this address.
**total_sent** | *integer* | Total amount of confirmed wei sent by this address.
**balance**	| *integer* | Balance of confirmed wei on this address. (i.e., for transactions whose confirmations > 0).
**unconfirmed_balance**	| *integer* | Balance of unconfirmed wei on this address. Can be negative (if unconfirmed transactions are just spending part of an address balance). Only unconfirmed transactions (haven't made it into a block) are included in this calculation.
**final_balance**	| *integer* |	Total balance of wei, including confirmed and unconfirmed transactions, for this address.
**n_tx** | *integer* | Number of confirmed transactions on this address. Only transactions that have made it into a block (confirmations > 0) are counted.
**unconfirmed_n_tx** | *integer* | Number of unconfirmed transactions for this address. Only unconfirmed transactions (confirmations == 0) are counted.
**final_n_tx** | *integer* | Final number of transactions, including confirmed and unconfirmed transactions, for this address.
**tx_url** | *url* | ***Optional*** To retrieve base URL transactions. To get the full URL, concatenate this URL with a transaction's hash.
**txs** | *array[[TX](#tx)]* | ***Optional***  Array of full transaction details associated with this address. Usually only returned from the [Address Detail Endpoint](#address-detail).
**txrefs** | *array[[TXRef](#txref)]* | ***Optional*** Array of transaction summaries for this address. Usually only returned from the standard [Address Endpoint](#address-endpoint).
**unconfirmed_txrefs** | *array[[TXRef](#txref)]* | ***Optional*** All unconfirmed transaction inputs and outputs for this address. Usually only returned from the standard [Address Endpoint](#address-endpoint).
**hasMore** | *bool* | ***Optional*** If *true*, then the Address object contains more transactions than shown. Useful for determining whether to poll the API for more transaction information.

## AddressKeychain

```shell
curl -sX POST https://api.blockcypher.com/v1/eth/main/addrs
{
	"private": "32605f9fbe224ebe621f88c33ae27099d4ce803e3b1cb5a1a3169790fd71beee",
	"public": "04d3e1b49ef16dcde23be535b621d15c83ff75ef097ae43f2ef7a98bcddbb402034ddb6a5ca781d7333ef988bb9a5cd98c1f407d8acf77a1a8a17a26899fa66be5",
	"address": "d543125acfc1e4d22f7f62aa5fbb2bb9a42bdb6d"
}
```

An AddressKeychain represents an associated collection of public and private keys alongside their respective Ethereum address. Generally returned and used with the [Generate Address Endpoint](#generate-address-endpoint).

Attribute | Type | Description
--------- | ---- | -----------
**address**	| *string* | Standard address representation.
**public** | *string* | Hex-encoded Public key.
**private**	| *string* | Hex-encoded Private key.
