---
title: Blockchain Developer API for Ethereum | Blockcypher
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

# Introduction to Ethereum

```shell
#  _               _                  
# |_) |  _   _ |  /     ._  |_   _  ._
# |_) | (_) (_ |< \_ \/ |_) | | (/_ | 
#                    /  |             
```

Welcome to [BlockCypher's](http://www.blockcypher.com/) Ethereum API documentation!

## Documentation Structure

```shell
man curl | grep -A 3 "DESCRIPTION"

DESCRIPTION
curl is a tool to transfer data from or to a server, using one of the supported protocols (DICT, FILE, FTP, FTPS, GOPHER, HTTP, HTTPS, IMAP, IMAPS, LDAP, LDAPS, POP3, POP3S, RTMP, RTSP, SCP, SFTP, SMB, SMBS, SMTP, SMTPS, TELNET and TFTP). The command is designed to work without user interaction.
```

In these docs you'll find everything you need to leverage BlockCypher for your Ethereum applications. For now, you'll see cURL examples for interacting with the Ethereum blockchain, but we'll add SDK examples as we support them. 

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
curl https://eth.api.call.here
```

Almost all resources exist under a given blockchain, and follow this pattern:

`https://api.blockcypher.com/$API_VERSION/$COIN/$CHAIN/`

Currently, there's only one version of the API (v1). For Ethereum, this is the resource you'll use:

Coin | Chain | Resource
---- | ----- | --------
Ethereum | Main | `api.blockcypher.com/ether/endpoint/here`

<aside class="notice">
Unless otherwise noted, all descriptions of direct HTTP requests will assume one of these base resources prepends it. But you can always see the full call in the cURL code sample.
</aside>

<aside class="success">
Our API <b>always</b> returns values in wei, the lowest non-divisible unit in Ethereum. As a friendly reminder, there are 10^18 wei in a single ether (1,000,000,000,000,000,000w = 1ETH).
</aside>

## Rate Limits and Tokens

```shell
# Adding your token as URL parameter
curl https://api.blockcypher.com/ether/path/here?token=$YOURTOKEN

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
# Batching blocks 5, 6, and 7
curl 'https://api.blockcypher.com/v1/btc/main/blocks/5;6;7'

[{
"hash": "000000003031a0e73735690c5a1ff2a4be82553b2a12b776fbd3a215dc8f778d",
"height": 6,
"chain": "BTC.main",
"total": 0,
"fees": 0,
"ver": 1,
"time": "2009-01-09T03:29:49Z",
...,
},
{
"hash": "000000009b7262315dbf071787ad3656097b892abffd1f95a1a022f896f533fc",
"height": 5,
"chain": "BTC.main",
"total": 0,
"fees": 0,
"ver": 1,
"time": "2009-01-09T03:23:48Z",
...,
},
{
"hash": "0000000071966c2b1d065fd446b1e485b2c9d9594acd2007ccbd5441cfc89444",
"height": 7,
"chain": "BTC.main",
"total": 0,
"fees": 0,
"ver": 1,
"time": "2009-01-09T03:39:29Z",
...,
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
