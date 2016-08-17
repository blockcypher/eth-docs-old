# Blockchain API

Blockchain docs here.

The first component---and highest level---of the BlockCypher Ethereum API allows you to query general information about Ethereum's blockchain and blocks.

If you're new to blockchains, you can think of the blockchain itself as an immutable, distributed ledger. Each block in the blockchain is like a "page" in the ledger containing information about transactions between parties. A great place to start understanding the mechanics behind blockchains is the [original Bitcoin whitepaper.](http://bitcoin.org/bitcoin.pdf) To get a handle on how Ethereum differs from Bitcoin, you can check the [Ethereum project whitepaper.](https://github.com/ethereum/wiki/wiki/White-Paper)

## Chain Endpoint

```shell
curl -s https://api.blockcypher.com/v1/eth/main
{
  "name": "ETH.main",
  "height": 1663350,
  "hash": "2dc3d6b080fbc78e576c8a0a7ccb52ca2af25b60c0df30ff82279fa41454729c",
  "time": "2016-06-08T00:46:10.101109766Z",
  "latest_url": "https://api.blockcypher.com/v1/eth/main/blocks/2dc3d6b080fbc78e576c8a0a7ccb52ca2af25b60c0df30ff82279fa41454729c",
  "previous_hash": "00709ed44e1ee2f2e5416c1e48bfd1a820e30aa14348b97d802d463f4ec940a6",
  "previous_url": "https://api.blockcypher.com/v1/eth/main/blocks/00709ed44e1ee2f2e5416c1e48bfd1a820e30aa14348b97d802d463f4ec940a6",
  "peer_count": 55,
  "unconfirmed_count": 11924,
  "high_gas_price": 40000000000,
  "medium_gas_price": 20000000000,
  "low_gas_price": 5000000000,
  "last_fork_height": 1661588,
  "last_fork_hash": "79075d95aacc6ac50dbdf58da044af396ca97e09cbb31527809579cc96f1c8a7"
}
```

General information about the Ethereum blockchain is available by GET-ing the [base resource](#restful-resources).

Resource | Method | Return Object
-------- | ------ | -------------
/ | GET | [Blockchain](#blockchain)

The returned object contains a litany of information, including its height, the time/hash of the latest block, and more. For Ethereum, the object includes information on gas_price, denoted in wei. Unlike Bitcoin, fees are determined by the computational execution cost of the transaction via gas; in order to prevent runaway contract execution, each operation in Ethereum's Virtual Machine requires a fixed/known amount of gas. While the amount of gas is fixed for a given EVM operation, the price of gas fluctuates based on market demand on the Ethereum blockchain (similar to Bitcoin fees). For more detail, check this [very helpful Stack Exchange explanation](https://ethereum.stackexchange.com/questions/3/what-is-meant-by-the-term-gas).

For more detailed information about the data returned, check the [Blockchain](#blockchain) object.

## Block Hash Endpoint

```shell
curl -s https://api.blockcypher.com/v1/eth/main/blocks/b35265965f6f14c5d692b2030922fce53067fc0e9d8c8490c31d4ecd3e46500c
{
  "hash": "b35265965f6f14c5d692b2030922fce53067fc0e9d8c8490c31d4ecd3e46500c",
  "height": 1564040,
  "chain": "ETH.main",
  "total": 20306306718874652,
  "fees": 1595580000000000,
  "size": 661,
  "ver": 0,
  "time": "2016-05-22T12:43:00Z",
  "received_time": "2016-05-22T12:43:00Z",
  "coinbase_addr": "4bb96091ee9d802ed039c4d1a5f6216f90f81b01",
  "relayed_by": "",
  "nonce": 2464233431647216841,
  "n_tx": 1,
  "prev_block": "e52d8cd9bf52374fd47e9d9ed81d2702f9e6352455f0ac07000bd3682b70cde7",
  "mrkl_root": "23f82aa013765c42fbee7f4e57c22befd7aece689834d0d2d6e25208713efb6a",
  "txids": [
    "8f39fb4940c084460da00a876a521ef2ba84ad6ea8d2f5628c9f1f8aeb395342"
  ],
  "internal_txids": [
    "dd107c8488867fd53c0aa3bf1d8a478a077ec67af75842d24f1a64eb44e4d902",
    "9a98678d209910f70bded624f2c5c18ed17d417752072d41c5d61b42c277348c",
    "ad959961f46663f785e1e0a0b2656960b5d27061097c5092c4e68964bfaf0041",
    "5a23d74a484ec58b211264888846be6d7428c33be93223396eff3626e036a7f4",
    "73906b2f1a1d7fd17c77684e5e811ae8710e4a032501090da264712db7a48e7a",
    "53ce88e1cf98b37fb64c16121b4641dde65290838ce0bde6cb1c319df3ef8ff9",
    "29bc4e7fa2db28b0a2ef9e7a5e073cc7532e609f752b2eb0b5731b0c6b926a2c",
    "4a9afc5a680917955e781b890688143f56badc76785af98753d5267270d7d809"
  ],
  "depth": 92613,
  "prev_block_url": "https://api.blockcypher.com/v1/eth/main/blocks/e52d8cd9bf52374fd47e9d9ed81d2702f9e6352455f0ac07000bd3682b70cde7",
  "tx_url": "https://api.blockcypher.com/v1/eth/main/txs/"
}
```

If you want more data on a particular block, you can use the Block Hash endpoint.

Resource | Method | Return Object
-------- | ------ | -------------
/blocks/$BLOCK_HASH | GET | [Block](#block)

Flag | Type | Effect
---- | ---- | ------
**txstart** | *integer* | Filters response to only include transaction hashes after **txstart** in the block.
**limit** | *integer* | Filters response to only include a maximum of **limit** transactions hashes in the block. Maximum value allowed is 500.

$BLOCK_HASH is a *string* representing the hash of the block you're interested in querying, for example:

`0000000000000000189bba3564a63772107b5673c940c16f12662b3e8546b412`

The returned object contains information about the block, including its height, the total amount of wei transacted within it, the number of transactions in it, transaction hashes listed in the canonical order in which they appear in the block, and more. For more detail on the data returned, check the [Block](#block) object.

## Block Height Endpoint

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
  "depth": 1656646,
  "prev_block_url": "https://api.blockcypher.com/v1/eth/main/blocks/1f1aed8e3694a067496c248e61879cda99b0709a1dfbacd0b693750df06b326e",
  "tx_url": "https://api.blockcypher.com/v1/eth/main/txs/"
}
```

You can also query for information on a block using its height, using the same resource but with a different variable type.

Resource | Method | Return Object
-------- | ------ | -------------
/blocks/$BLOCK_HEIGHT | GET | [Block](#block)

Flag | Type | Effect
---- | ---- | ------
**txstart** | *integer* | Filters response to only include transaction hashes after **txstart** in the block.
**limit** | *integer* | Filters response to only include a maximum of **limit** transactions hashes in the block. Maximum value allowed is 500.

BLOCK_HEIGHT is an *integer* representing the height of the block you're interested in querying, for example:

`1500000`

As above, the returned object contains information about the block, including its hash, the total amount of wei transacted within it, the number of transactions in it, transaction hashes listed in the canonical order in which they appear in the block, and more. For more detail on the data returned, check the [Block](#block) object.

<aside class="warning">
With recent blocks, $BLOCK_HEIGHT is not always a unique identifier, due to the possibility of soft forks/orphans and the nature of the consensus model with blockchains. If you're querying blocks with <b>depth above 10</b> (i.e., there are more than 10 blocks ahead of your target) the height should be a safe identifier.
</aside>
