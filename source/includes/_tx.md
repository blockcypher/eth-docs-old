# Transaction API

BlockCypher's Ethereum Transaction API allows you to look up information about unconfirmed transactions, query transactions based on hash, and create and propagate your own transactions.

Unlike Bitcoin, Ethereum has no concept of [UTXOs](https://bitcoin.org/en/glossary/unspent-transaction-output). Instead, addresses have a running balance, and use an ever-increasing sequence/nonce for every transaction to maintain proper address state. You can read more about [this design decision here.](https://github.com/ethereum/wiki/wiki/Design-Rationale#accounts-and-not-utxos) Transaction signing and generation are also different in Ethereum, but for basic transactions, the process is very similar to our Bitcoin API.

## Transaction Hash Endpoint

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
  "confirmations": 94919,
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

The Transaction Hash Endpoint returns detailed information about a given transaction based on its hash.

Resource | Method | Return Object
-------- | ------ | -------------
/txs/$TXHASH | GET | [TX](#tx)

TXHASH is a *string* representing the hex-encoded transaction hash you're interested in querying, for example:

`8f39fb4940c084460da00a876a521ef2ba84ad6ea8d2f5628c9f1f8aeb395342`

The returned object contains detailed information about the transaction, including the value transfered, fees collected, date received, any scripts associated with an output, and more.

## Unconfirmed Transactions Endpoint

```shell
curl -s https://api.blockcypher.com/v1/eth/main/txs
[
  {
    "block_height": -1,
    "block_index": 0,
    "hash": "1f5bd529e72f6aa5724b6a3971781909e5156dc60058ccdfea15419bef25f5dd",
    "addresses": [
      "70d84f9b00556bb41c04aa0205506345f43ba746",
      "f7b0b916b2849255a61127fc4a42b9dfeaddf7d6"
    ],
    "total": 972909140000000000,
    "fees": 1260000000000000,
    "size": 110,
    "relayed_by": "",
    "received": "2016-06-07T07:47:40.14486539Z",
    "ver": 0,
    "double_spend": false,
    "vin_sz": 1,
    "vout_sz": 1,
    "confirmations": 0,
    "inputs": [
      {
        "sequence": 19,
        "addresses": [
          "70d84f9b00556bb41c04aa0205506345f43ba746"
        ]
      }
    ],
    "outputs": [
      {
        "value": 972909140000000000,
        "addresses": [
          "f7b0b916b2849255a61127fc4a42b9dfeaddf7d6"
        ]
      }
    ]
  },
  {
    "block_height": -1,
    "block_index": 0,
    "hash": "1bd833698337fce44a7feedf6bbe78c90ada088389ebafcad0b56901def3c989",
    "addresses": [
      "a1b5f95be71ffa2f86adefcaa0028c46fe825161",
      "b7443c8bdff977234ed9394169f181f9e9851422"
    ],
    "total": 1000000000000000000,
    "fees": 2420000000000000,
    "size": 111,
    "relayed_by": "",
    "received": "2016-06-07T07:47:38.710195289Z",
    "ver": 0,
    "double_spend": false,
    "vin_sz": 1,
    "vout_sz": 1,
    "confirmations": 0,
    "inputs": [
      {
        "sequence": 68,
        "addresses": [
          "a1b5f95be71ffa2f86adefcaa0028c46fe825161"
        ]
      }
    ],
    "outputs": [
      {
        "value": 1000000000000000000,
        "addresses": [
          "b7443c8bdff977234ed9394169f181f9e9851422"
        ]
      }
    ]
  },
	...
]
```

The Unconfirmed Transactions Endpoint returns an array of the latest transactions that haven't been included in any blocks.

Resource | Method | Return Object
-------- | ------ | -------------
/txs | GET | Array[[TX](#TX)]

The returned object is an array of transactions that haven't been included in blocks, arranged in reverse chronological order (latest is first, then older transactions follow).
