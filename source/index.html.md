---
title: Blockchain Developer API for Ethereum | BlockCypher
seo_description: Build Ethereum applications easily with our web APIs and callbacks. High throughputs, linear scaling, low-latency. Over 99.99% uptime with no single point of failure.

language_tabs:
  - shell: cURL

toc_footers:
  - <a href='https://accounts.blockcypher.com/'>Sign Up/Login</a>

includes:
  - objects
  - blockchain
  - address
  - tx
---

# Introduction

```shell
#   ^
#  / \    _               _
# / ^ \  |_) |  _   _ |  /     ._  |_   _  ._
#<     > |_) | (_) (_ |< \_ \/ |_) | | (/_ |
# \ v /                     /  |
#  \ /
#   v
```

Welcome to [BlockCypher's](http://www.blockcypher.com/) Ethereum API documentation! If you're familiar with our Bitcoin endpoints, you'll feel right at home with our Ethereum API. However, there are a few differences, and they stem in part from the fundamental differences between Bitcoin and Ethereum. In the most abstract sense, Bitcoin and Ethereum are cousins; they both have "blocks" of transactions linked together into a chain, they both use Proof of Work to reach consensus (for now, as Ethereum plans to move to Proof of Stake in a future release), they are both about decentralizing trust. But that's where the similarities end. Here are just some of Ethereum's differences:

- There are no UTXOs, only "accounts" (of two varieties)
- Block time is significantly shorter; 15 second target instead of 10 minutes
- Miners get rewards for including references to orphan blocks (so called "uncle blocks")
- Ethereum's scripting language is far more expressive than Bitcoin's; typically advanced transactions/contracts are constructed using a higher level language then converted into EVM bytecode
- Ethereum has no strict cap on monetary supply (ether supply)
- Transactions cost "gas" to run (denominated in ether) depending on how computationally expensive they are to run. Blocks also have total gas limits to prevent runaway computation/keep the network decentralized.

In a nutshell, Bitcoin is about decentralized, trust-minimizing, sound money. Ethereum is about decentralized, trust-minimizing, sound computation. Much more detail about these differences can be read at the [Ethereum Wiki here.](https://github.com/ethereum/wiki/wiki/Design-Rationale) You can find more information at the [project's webpage as well.](https://ethereum.org/)

<aside class="warning">
As with our other coin/chain nodes, we rebuilt an Ethereum node from the ground up to support much higher scale and throughput, but it's a lot newer than our other implementations. THIS IS VERY MUCH A BETA. Things may break/it might not be suitable for production applications. 
</aside>

<aside class="notice">
We don't have the full suite of API endpoints compared to Bitcoin, but we are always adding more as our implementation matures. Check these docs for the latest features.
</aside>

## Documentation Structure

```shell
man curl | grep -A 3 "DESCRIPTION"

DESCRIPTION
curl is a tool to transfer data from or to a server, using one of the supported protocols (DICT, FILE, FTP, FTPS, GOPHER, HTTP, HTTPS, IMAP, IMAPS, LDAP, LDAPS, POP3, POP3S, RTMP, RTSP, SCP, SFTP, SMB, SMBS, SMTP, SMTPS, TELNET and TFTP). The command is designed to work without user interaction.
```

In these docs you'll find everything you need to start working with Ethereum with BlockCypher. For now, you'll see cURL examples for interacting with the Ethereum blockchain, but we'll add SDK examples as we support them.

### Section Summaries

- [Objects:](#objects) An overview of all the objects in the BlockCypher Ethereum API, alongside detailed descriptions of every field.
- [Blockchain API:](#blockchain-api) Endpoints to query general information about the Ethereum blockchain and its blocks.
- [Address API:](#address-api) Query information about addresses, and generate addresses.
- [Transaction API:](#transaction-api) Information about transactions, how to generate/send your own.

### Changelog and Errors

Our documentation is powered by [GitHub Pages](https://pages.github.com/) and [Slate](https://github.com/tripit/slate), which makes viewing changes as simple as checking the [git commit history](https://github.com/blockcypher/eth-docs/commits/master). If there's an error or you'd like to suggest a change, please consider submitting a pull request to benefit the broader BlockCypher community.

## API Versions

All API calls are versioned, and the current BlockCypher API is v1. **The BlockCypher Ethereum API is currently in beta**, and we hope not to introduce any breaking changes, but until it's out of beta we can't make that guarantee until it's stable.

## RESTful Resources

```shell
curl -s https://api.blockcypher.com/v1/eth/main
{
  "name": "ETH.main",
  "height": 1663353,
  "hash": "863dda1124f2b438c607f5b8d00e8511f6f8d206b21aad3b9c460b8c5221e31b",
  "time": "2016-06-08T00:46:34.795856213Z",
  "latest_url": "https://api.blockcypher.com/v1/eth/main/blocks/863dda1124f2b438c607f5b8d00e8511f6f8d206b21aad3b9c460b8c5221e31b",
  "previous_hash": "783aa3ef1b45121ee5bc33acb6c5986d6132d04cf20c85ba256b155b2c196006",
  "previous_url": "https://api.blockcypher.com/v1/eth/main/blocks/783aa3ef1b45121ee5bc33acb6c5986d6132d04cf20c85ba256b155b2c196006",
  "peer_count": 52,
  "unconfirmed_count": 11924,
  "high_gas_price": 40000000000,
  "medium_gas_price": 20000000000,
  "low_gas_price": 5000000000,
  "last_fork_height": 1661588,
  "last_fork_hash": "79075d95aacc6ac50dbdf58da044af396ca97e09cbb31527809579cc96f1c8a7"
}
```

Almost all resources exist under a given blockchain, and follow this pattern:

`https://api.blockcypher.com/$API_VERSION/$COIN/$CHAIN/`

Currently, there's only one version of the API (v1). For Ethereum, this is the resource you'll use:

Coin | Chain | Resource
---- | ----- | --------
Ethereum | Main | `api.blockcypher.com/v1/eth/main`

<aside class="notice">
Unless otherwise noted, all descriptions of direct HTTP requests will assume one of these base resources prepends it. But you can always see the full call in the cURL code sample.
</aside>

<aside class="success">
Our API <b>always</b> returns values in wei, the lowest non-divisible unit in Ethereum. As a friendly reminder, there are 10^18 wei in a single ether (1,000,000,000,000,000,000w = 1ETH). Some say it's wei too much subdivision, but we disagree.
</aside>

## Rate Limits and Tokens

```shell
# Adding your token as URL parameter
curl https://api.blockcypher.com/v1/eth/main?token=YOURTOKEN

# Checking your token's limits
curl https://api.blockcypher.com/v1/tokens/YOURTOKEN
{
"token": "YOURTOKEN",
"limits": {
	"api/hour": 10000,
	"api/second": 500,
	"hooks/hour": 5000,
	"confidence/hour": 1000,
	"hooks": 5000,
	"payments": 5000
},
"hits": {
	"api/hour": 280,
	"hooks/hour": 240,
	"confidence/hour": 100
}
}
# These are quite above the default limits, but if you'd like them, reach out at contact@blockcypher.com ;)
```

We want everyone to try BlockCypher with as little friction as possible, which is why you don't need a token for any read-only **GET** calls. Please [register for a user token](http://accounts.blockcypher.com/) if you want to use **POST** and **DELETE** calls. Once you have your token, you can append it to all your requests like any other URL parameter.

We do rate-limit our free tier, with or without a token (though tokens are required for Confidence lookups, WebHooks/Sockets, Payments, and any **POST** or **DELETE** calls):

- Classic requests, up to 3 requests/sec and 200 requests/hr
- WebHooks and WebSockets, up to 200 requests/hr
- WebHooks and Payments, up to 200 stored on our servers
- Confidence lookups, up to 15 requests/hour

<aside class="warning">
If you exceed these limits, your requests will return an HTTP Status Code 429!
</aside>

On [the accounts page](https://accounts.blockcypher.com/), you'll find paid plans starting at $75 a month (with a 10% discount if you pay with Bitcoin). To request higher limits or SLAs beyond what's offered on the accounts page, please [email us.](mailto:contact@blockcypher.com)

You can check your current limits and usage via a **GET** on the following endpoint, outside of our normal coin/chain pattern:

`https://api.blockcypher.com/v1/tokens/YOURTOKEN`

## Batching

```shell
# Batching blocks 5, 6, and 7 from Ethereum
curl 'https://api.blockcypher.com/v1/eth/main/blocks/5;6;7'
[{
  "hash": "f37c632d361e0a93f08ba29b1a2c708d9caa3ee19d1ee8d2a02612bffe49f0a9",
  "height": 5,
  "chain": "ETH.main",
  "total": 0,
  "fees": 0,
  "size": 537,
  "ver": 0,
  "time": "2015-07-30T15:28:03Z",
  "received_time": "2015-07-30T15:28:03Z",
  "coinbase_addr": "05a56e2d52c817161883f50c441c3228cfe54d9f",
  "relayed_by": "",
  "nonce": 18134254966252788979,
  "n_tx": 0,
  "prev_block": "23adf5a3be0f5235b36941bcb29b62504278ec5b9cdfa277b992ba4a7a3cd3a2",
  "mrkl_root": "4470f3dc1cc8097394a4ae85302eac3368462b3c1cfa523ffca942c1dd478220",
  "txids": [],
  "depth": 1656648,
  "prev_block_url": "https://api.blockcypher.com/v1/eth/main/blocks/23adf5a3be0f5235b36941bcb29b62504278ec5b9cdfa277b992ba4a7a3cd3a2",
  "tx_url": "https://api.blockcypher.com/v1/eth/main/txs/"
},
{
  "hash": "1f1aed8e3694a067496c248e61879cda99b0709a1dfbacd0b693750df06b326e",
  "height": 6,
  "chain": "ETH.main",
  "total": 0,
  "fees": 0,
  "size": 537,
  "ver": 0,
  "time": "2015-07-30T15:28:27Z",
  "received_time": "2015-07-30T15:28:27Z",
  "coinbase_addr": "0193d941b50d91be6567c7ee1c0fe7af498b4137",
  "relayed_by": "",
  "nonce": 8915216988711600153,
  "n_tx": 0,
  "prev_block": "f37c632d361e0a93f08ba29b1a2c708d9caa3ee19d1ee8d2a02612bffe49f0a9",
  "mrkl_root": "c211464e6957ae62a33c2146cf36371222ea4557c42efe9c618971e09d056aae",
  "txids": [],
  "depth": 1656647,
  "prev_block_url": "https://api.blockcypher.com/v1/eth/main/blocks/f37c632d361e0a93f08ba29b1a2c708d9caa3ee19d1ee8d2a02612bffe49f0a9",
  "tx_url": "https://api.blockcypher.com/v1/eth/main/txs/"
},
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
}]
```

All endpoints that can retrieve a single [Object](#objects) can be batched to return multiple objects. If you're cURLing the API directly, batching simply requires appending each identifier to the previous one using a semicolon (check the code pane for an example). The results are aggregated in a JSON array. 

<aside class="notice">
The maximum number of elements that can be batched in a single call is 100.
</aside>

<aside class="notice">
As you may have noticed from the examples, batched calls don't necessarily respect ordering, especially for bigger batches. But this shouldn't matter, as the requested identifiers are always present in the returned objects. 
</aside>

When cURLing BlockCypher, batching also works when the identifiers aren't the last part of the URL; e.g., this URL will return the balances of three separate addresses:

`https://api.blockcypher.com/v1/btc/main/addrs/1J38WorKngZLJvA7qMin9g5jqUfTQUBZNE;1JP8FqoXtCMrR1sZc2McLWmHxENox1Y1PV;1ENn7XmqXNnReiQEFHhBGzfiv5gAyBj7r1/balance`

<aside class="warning">
With regard to rate limits, each individual batch call counts as a request; for example, if you request 3 addresses in a batch, you're still using 3 API calls of resources on our end. The big advantage to batching is that you avoid 3 separate round-trip calls/reduce latency. Since the default, non-registered <a href="#rate-limits-and-tokens">rate limit</a> per second is 3, larger batches require a paid API token. To use larger batches <a href="https://accounts.blockcypher.com/">please register.</a>
</aside>
