# Address API

BlockCypher's Ethereum Address API allows you to look up information about public Ethereum addresses/accounts and generate your own low-value Ethereum addresses/public-private key pairs.

If you're new to blockchains, you can think of public addresses as similar to bank account numbers in a traditional ledger. The biggest differences:

- Anyone can generate a public address themselves (through [ECDSA](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm) in Ethereum by default). No single authority is needed to generate new addresses; it's just public-private key cryptography.
- There are two kinds of addresses in Ethereum: external addresses and contract addresses. Contract addresses are controlled by embedded scripts within the Ethereum blockchain, while external addresses are controlled in a similar manner to Bitcoin.

## Address Balance Endpoint

```shell
curl -s https://api.blockcypher.com/v1/eth/main/addrs/738d145faabb1e00cf5a017588a9c0f998318012/balance
{
  "address": "738d145faabb1e00cf5a017588a9c0f998318012",
  "total_received": 9762206505909057760,
  "total_sent": 6916970312523512365,
  "balance": 2845236193385545395,
  "unconfirmed_balance": 0,
  "final_balance": 2845236193385545395,
  "n_tx": 702,
  "unconfirmed_n_tx": 0,
  "final_n_tx": 702
}
```

The Address Balance Endpoint is the simplest---and fastest---method to get a subset of information on a public address.

Resource | Method | Return Object
-------- | ------ | -------------
/addrs/$ADDRESS/balance | GET | [Address](#address)

ADDRESS is a *string* representing the public address you're interested in querying, for example:

`738d145faabb1e00cf5a017588a9c0f998318012`

The returned object contains information about the address, including its balance in wei and the number of transactions associated with it. The endpoint omits any detailed transaction information, but if that isn't required by your application, then it's the fastest and preferred way to get public address information.

## Address Endpoint

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

The Address Endpoint returns more information about an address' transactions than the [Address Balance Endpoint](#address-balance-endpoin), but sacrifices some response speed in the process.

Resource | Method | Return Object
-------- | ------ | -------------
/addrs/$ADDRESS | GET | [Address](#address)

Flag | Type | Effect
---- | ---- | ------
**before** | *integer* | Filters response to only include transactions below **before** height in the blockchain.
**after** | *integer* | Filters response to only include transactions above **after** height in the blockchain.
**limit** | *integer* | **limit** sets the minimum number of returned TXRefs; there can be less if there are less than **limit** TXRefs associated with this address, but there can be more in the rare case of more TXRefs in the block at the bottom of your call. This ensures paging by block height never misses TXRefs. Defaults to 200, maximum is 2000.
**confirmations** | *integer* | If set, only returns the **balance** and TXRefs that have *at least* this number of **confirmations**.

ADDRESS is a *string* representing the public address (or wallet/HD wallet name) you're interested in querying, for example:

`738d145faabb1e00cf5a017588a9c0f998318012`

The returned object contains information about the address, including its balance in wei, the number of transactions associated with it, and transaction summaries in descending order by block height.
