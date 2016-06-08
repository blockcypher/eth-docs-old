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

## Creating Transactions

Using BlockCypher's API, you can push transactions to Ethereum in one of two ways:

- Use another Ethereum library to create your transactions, then [push them using our raw-transaction-endpoint](#push-raw-transaction-endpoint)
- Use our two-endpoint process outlined below, wherein we generate a [TXSkeleton](#txskeleton) based on your input address, output address, and value to transfer.

In either case, for security reasons, we never take possession of your private keys. 

<aside class="warning">
Always use HTTPS for creating and pushing transactions.
</aside>

```shell
curl -sd '{"inputs":[{"addresses": ["add42af7dd58b27e1e6ca5c4fdc01214b52d382f"]}],"outputs":[{"addresses": ["884bae20ee442a1d53a1d44b1067af42f896e541"], "value": 4200000000000000}]}' https://api.blockcypher.com/v1/eth/main/txs/new?token=YOURTOKEN

{
  "tx": {
    "block_height": -1,
    "block_index": 0,
    "hash": "9403244c6bcfe4063e7fb33138b02881d7a72ccdbd9c04d9abdff2b432d67ada",
    "addresses": [
      "add42af7dd58b27e1e6ca5c4fdc01214b52d382f",
      "884bae20ee442a1d53a1d44b1067af42f896e541"
    ],
    "total": 4200000000000000,
    "fees": 861000000000000,
    "size": 44,
    "gas_used": 0,
    "gas_price": 41000000000,
    "relayed_by": "",
    "received": "2016-06-08T01:32:35.573921633Z",
    "ver": 0,
    "double_spend": false,
    "vin_sz": 1,
    "vout_sz": 1,
    "inputs": [
      {
        "sequence": 0,
        "addresses": [
          "add42af7dd58b27e1e6ca5c4fdc01214b52d382f"
        ]
      }
    ],
    "outputs": [
      {
        "value": 4200000000000000,
        "addresses": [
          "884bae20ee442a1d53a1d44b1067af42f896e541"
        ]
      }
    ]
  },
  "tosign": [
    "a83f5bea598e0d217a03a2646d6c49edb2e99daf4537b2c09b008df76b77acec"
  ]
}
```

### New Transaction Endpoint

To use BlockCypher's two-endpoint transaction creation tool, first you need to provide the input address, output address, and value to transfer (in wei). Provide this in a partially-filled out [TX](#tx) request object.

Resource | Method | Request Object | Return Object
-------- | ------ | -------------- | -------------
/txs/new | POST | [TX](#tx) | [TXSkeleton](#txskeleton)

As you can see from the code example, you only need to provide a single public address within the **addresses** array of both the **input** and **output** of your [TX](#tx) request object. You also need to fill in the **value** with the amount you'd like to transfer from one address to another. Note that we only accept a single input and output **address** per Ethereum's transaction model, and **tosign** only returns a single element in its array; we use arrays for parity with our Bitcoin API.

As a return object, you'll receive a [TXSkeleton](#txskeleton) containing a slightly-more complete [TX](#tx) alongside data you need to sign in the **tosign** array. You'll need this object for the next steps of the transaction creation process.

<aside class="warning">
The <a href="#txskeleton">TXSkeleton</a> returned by this endpoint may contain some data that's temporary or incomplete, like the <b>hash</b>, and the <b>gas used</b> fields. This is by design, as the final <a href="#tx">TX</a> can only be computed once signed data has been added. Do not rely on these fields until they are returned and sent to the network via the Send Transaction Endpoint outlined below.
</aside>


```shell
# next, you sign the data returned in the tosign array locally
# here we're using our signer tool (https://github.com/blockcypher/btcutils/tree/master/signer), but any ECDSA secp256k1 signing tool should work (Ethereum uses the same ECDSA curve as Bitcoin for transaction signatures)
# $PRIVATEKEY here is a hex-encoded private key corresponding to the input from address add42af7dd58b27e1e6ca5c4fdc01214b52d382f

./signer a83f5bea598e0d217a03a2646d6c49edb2e99daf4537b2c09b008df76b77acec $PRIVATEKEY 
3045022100bc04ce017622f9830f955dbd8fafb65c5a72306a674711e507200f5f198954c90220581bc05e2658c258a985d914a158f89f44144a2e082837955b218d12a43a6a38
```
### Locally Sign Your Transaction

With your [TXSkeleton](#txskeleton) returned from the New Transaction Endpoint, you now need to use your private key to sign the data provided in the **tosign** array.

Digital signing can be a difficult process, and is where the majority of issues arise when dealing with cryptocurrency transactions. Ethereum uses the same elliptic curve as Bitcoin (secp256k1), so any Bitcoin signing library that fits with your workflow should suffice. If you want to experiment with client-side signing, consider using our [signer tool](https://github.com/blockcypher/btcutils/tree/master/signer).

<aside class="notice">
One of the most common errors in the signing process is a data format mismatch. We always return and expect hex-encoded data, but oftentimes, standard signing libraries require byte arrays. Remember to convert your data, and always send hex-encoded signatures to BlockCypher.
</aside>

```shell
# the request body is truncated because it's huge, but it's the same as the returned object from above plus the signature
curl -sd '{"tx": {...}, "tosign": [ "a83f5bea598e0d217a03a2646d6c49edb2e99daf4537b2c09b008df76b77acec" ], "signatures": [ "3045022100bc04ce017622f9830f955dbd8fafb65c5a72306a674711e507200f5f198954c90220581bc05e2658c258a985d914a158f89f44144a2e082837955b218d12a43a6a38" ]}' https://api.blockcypher.com/v1/eth/main/txs/send?token=YOURTOKEN

{
  "tx": {
    "block_height": -1,
    "block_index": 0,
    "hash": "85725e97d83ec7ce3888ca74d68b7820640f80d6380b9e1c565f61f5409acf50",
    "addresses": [
      "add42af7dd58b27e1e6ca5c4fdc01214b52d382f",
      "884bae20ee442a1d53a1d44b1067af42f896e541"
    ],
    "total": 4200000000000000,
    "fees": 861000000000000,
    "size": 109,
    "gas_used": 0,
    "gas_price": 41000000000,
    "relayed_by": "",
    "received": "2016-06-08T02:38:59.691331763Z",
    "ver": 0,
    "double_spend": false,
    "vin_sz": 1,
    "vout_sz": 1,
    "inputs": [
      {
        "sequence": 0,
        "addresses": [
          "add42af7dd58b27e1e6ca5c4fdc01214b52d382f"
        ]
      }
    ],
    "outputs": [
      {
        "value": 4200000000000000,
        "addresses": [
          "884bae20ee442a1d53a1d44b1067af42f896e541"
        ]
      }
    ]
  }
}
```

### Send Transaction Endpoint

Once you've finished signing the **tosign** data locally, put that (hex-encoded) data into the **signatures** array of the [TXSkeleton](#txskeleton). Unlike Bitcoin, you don't need to include the signing accounts public key, as on Ethereum this is derived using the **tosign** data and **signature** data. But you must include the **tosign** data in addition to the **signatures** array for that derivation to work.

Resource | Method | Request Object | Return Object
-------- | ------ | -------------- | -------------
/txs/send | POST | [TXSkeleton](#txskeleton) | [TXSkeleton](#txskeleton)

If the transaction was successful, you'll receive a [TXSkeleton](#TXskeleton) with the completed [TX](#tx) (which contains its final **hash**) and an HTTP Status Code 201.

## Decode Raw Transaction Endpoint

```shell
curl -sd '{"tx":"f86b808504e3b2920082520894add42af7dd58b27e1e6ca5c4fdc01214b52d382f870bdccd84e7b000801ba0b86360f1c2d2b38421a80e71bf4cf54371bc9aa62f81c925484c6557b44b13f1a07b5690150c10a3947225fb612162c90ccfaefde99f7d363a8013e3eead0e55dd"}' https://api.blockcypher.com/v1/eth/main/txs/decode?token=YOURTOKEN
{
  "block_height": -1,
  "block_index": 0,
  "hash": "a88cca4dd97e028d7199028888156c4dad9936a2cbdfe8262fb12a252e16d4f1",
  "addresses": [
    "7ea7eb1c8b0fba77964c561f9b7494a87534aa15",
    "add42af7dd58b27e1e6ca5c4fdc01214b52d382f"
  ],
  "total": 3339000000000000,
  "fees": 441000000000000,
  "size": 109,
  "gas_used": 0,
  "gas_price": 21000000000,
  "relayed_by": "54.213.170.147",
  "received": "2016-06-08T06:18:08.181893896Z",
  "ver": 0,
  "double_spend": false,
  "vin_sz": 1,
  "vout_sz": 1,
  "confirmations": 0,
  "inputs": [
    {
      "sequence": 0,
      "addresses": [
        "7ea7eb1c8b0fba77964c561f9b7494a87534aa15"
      ]
    }
  ],
  "outputs": [
    {
      "value": 3339000000000000,
      "addresses": [
        "add42af7dd58b27e1e6ca5c4fdc01214b52d382f"
      ]
    }
  ]
}
```
We also offer the ability to decode raw transactions without sending propagating them to the network; perhaps you want to double-check another client library or confirm that another service is sending proper transactions. 

Resource | Method | Request Object | Return Object
-------- | ------ | -------------- | -------------
/txs/decode | POST | {"tx":$TXHEX} | [TX](#tx)

$TXHEX is a hex-encoded raw representation of your transaction, for example:

`f86b808504e3b2920082520894add42af7dd58b27e1e6ca5c4fdc01214b52d382f870bdccd84e7b000801ba0b86360f1c2d2b38421a80e71bf4cf54371bc9aa62f81c925484c6557b44b13f1a07b5690150c10a3947225fb612162c90ccfaefde99f7d363a8013e3eead0e55dd`

If it succeeds, you'll receive your decoded [TX](#tx) object.

## Push Raw Transaction Endpoint

```shell
curl -sd '{"tx":"f86b808504e3b2920082520894add42af7dd58b27e1e6ca5c4fdc01214b52d382f870bdccd84e7b000801ba0b86360f1c2d2b38421a80e71bf4cf54371bc9aa62f81c925484c6557b44b13f1a07b5690150c10a3947225fb612162c90ccfaefde99f7d363a8013e3eead0e55dd"}' https://api.blockcypher.com/v1/eth/main/txs/push?token=YOURTOKEN
{
  "block_height": -1,
  "block_index": 0,
  "hash": "a88cca4dd97e028d7199028888156c4dad9936a2cbdfe8262fb12a252e16d4f1",
  "addresses": [
    "7ea7eb1c8b0fba77964c561f9b7494a87534aa15",
    "add42af7dd58b27e1e6ca5c4fdc01214b52d382f"
  ],
  "total": 3339000000000000,
  "fees": 441000000000000,
  "size": 109,
  "gas_used": 0,
  "gas_price": 21000000000,
  "relayed_by": "54.213.170.147",
  "received": "2016-06-08T06:18:08.181893896Z",
  "ver": 0,
  "double_spend": false,
  "vin_sz": 1,
  "vout_sz": 1,
  "confirmations": 0,
  "inputs": [
    {
      "sequence": 0,
      "addresses": [
        "7ea7eb1c8b0fba77964c561f9b7494a87534aa15"
      ]
    }
  ],
  "outputs": [
    {
      "value": 3339000000000000,
      "addresses": [
        "add42af7dd58b27e1e6ca5c4fdc01214b52d382f"
      ]
    }
  ]
}
```

If you'd prefer to use your own transaction library instead of the recommended path of our two-endpoint [transaction generation](#creating-transactions) we're still happy to help you propagate your raw transactions. Simply send your raw hex-encoded transaction to this endpoint and we'll leverage our well-connected network to propagate your transaction faster than anywhere else.

Resource | Method | Request Object | Return Object
-------- | ------ | -------------- | -------------
/txs/push | POST | {"tx":$TXHEX} | [TX](#tx)

$TXHEX is a hex-encoded raw representation of your transaction, for example:

`f86b808504e3b2920082520894add42af7dd58b27e1e6ca5c4fdc01214b52d382f870bdccd84e7b000801ba0b86360f1c2d2b38421a80e71bf4cf54371bc9aa62f81c925484c6557b44b13f1a07b5690150c10a3947225fb612162c90ccfaefde99f7d363a8013e3eead0e55dd`

If it succeeds, you'll receive a decoded [TX](#tx) object and an HTTP Status Code 201. You can then use the **hash** to track its progress on the network.
