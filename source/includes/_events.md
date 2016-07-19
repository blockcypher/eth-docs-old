# Events and Hooks

Ethereum is a highly transactional system. Many usage patterns require knowing when an event occurs: i.e., when a transaction is included into a block, when an address is updated, etc. Instead of requiring you to continuously poll resources, we provide push APIs to facilitate those use cases, and support WebHooks for that express purpose.

<aside class="notice">
Please note: WebSockets are not supported on Ethereum at the moment.
</aside>

## Types of Events

We support a number of different event types, and you can filter your notification requests depending on how you structure your [Event](#event) request object. 

Event | Description
----- | -----------
*unconfirmed-tx* | Triggered for every new transaction BlockCypher receives before it's confirmed in a block; basically, for every unconfirmed transaction. The payload is an unconfirmed [TX](#tx).
*new-block* | Triggered for every new block. The payload is a [Block](#block).
*confirmed-tx* | Triggered for every new transaction making it into a new block; in other words, for every first transaction confirmation. This is equivalent to listening to the *new-block* event and fetching each transaction (or filtered transaction) in the new [Block](#block). The payload is a confirmed [TX](#tx).
*tx-confirmation* | Simplifies listening to confirmations on all transactions for a given address up to a provided threshold. Sends first the unconfirmed transaction and then the transaction for each confirmation. Use the **confirmations** property within the [Event](#event) to manually specify the number of confirmations desired (maximum 10, defaults to 6). The payload is a [TX](#tx).
*double-spend-tx* | Triggered any time a double spend is detected by BlockCypher. The payload is the [TX](#tx) that triggered the event; the hash of the transaction that it's trying to double spend is included in its **double_spend_tx** property.

<aside class="notice">
Events like <i>unconfirmed-tx</i> can produce a lot of requests, especially without any filtering. To avoid rate-limiting, please <a href="http://accounts.blockcypher.com/">register for a token.</a>
</aside>

## Using WebHooks

For WebHooks, the JSON [Event](#event) should be sent using a POST request to the "create webhook endpoint" and include a **url** property to denote where payloads should be delivered. The [TX](#tx) or [Block](#block) associated with the [Event](#event) will be POSTed to the provided **url**. The POSTed payload will also include *X-EventType* and *X-EventId* metadata in the HTTP header specifying the **event** type and **id** of the WebHook which triggered the payload.

We retry individual payloads to your **url** five times; if one fails, we wait exponentially between retries: 1 second, 2s, 4s, 8s, 16s. In order to protect against stale callback **url**s, your [Event](#event) will be deleted if it reaches 50 aggregate **callback_errors** from failed payloads.

<aside class="warning">
To prevent eavesdropping, we recommend securing your callback <b>url</b> by using SSL and providing a <i>secret</i> parameter appended to the <a href="#event">Event</a> request. We POST the payload to the unaltered <b>url</b>, which allows you to check on your server that the parameter was not modified.
</aside>

<aside class="notice">
Testing WebHooks can be tricky; we recommend using <a href="http://requestb.in/">requestb.in</a> to set up a temporary server to test your events.
</aside>

### Create WebHook Endpoint

```shell
curl -sd '{"event": "unconfirmed-tx", "address": "c5beef03aaa9548210b5550a62f7756efaa08ba3", "url": "https://my.domain.com/callbacks/new-tx"}' https://api.blockcypher.com/v1/eth/main/hooks?token=YOURTOKEN
{
  "id": "ce83d08f-7b86-4a6a-9b41-85507648f9c7",
  "token": "YOURTOKEN",
  "url": "https://my.domain.com/callbacks/new-tx",
  "callback_errors": 0,
  "address": "c5beef03aaa9548210b5550a62f7756efaa08ba3",
  "event": "unconfirmed-tx",
  "filter": "addr=c5beef03aaa9548210b5550a62f7756efaa08ba3\u0026event=unconfirmed-tx"
}
```

Using a partially filled out [Event](#event), you can create a WebHook using this resource. Check the [Event object description](#event) and [types of events](#types-of-events) to understand the available options.

Resource | Method | Request Object | Return Object
-------- | ------ | -------------- | -------------
/hooks | POST | [Event](#event) | [Event](#event)

If successful, it will return the [Event](#event) with a newly generated **id**.

### List WebHooks Endpoint

```shell
curl -s https://api.blockcypher.com/v1/eth/main/hooks?token=YOURTOKEN
[
  {
    "id": "ce83d08f-7b86-4a6a-9b41-85507648f9c7",
    "token": "YOURTOKEN",
    "url": "https://my.domain.com/callbacks/new-tx",
    "callback_errors": 0,
    "address": "c5beef03aaa9548210b5550a62f7756efaa08ba3",
    "event": "unconfirmed-tx",
    "filter": "addr=c5beef03aaa9548210b5550a62f7756efaa08ba3\u0026event=unconfirmed-tx"
  }
]
```

This resource lists your currently active events, according to the base resource and $YOURTOKEN.

Resource | Method | Return Object
-------- | ------ | -------------
/hooks?token=$YOURTOKEN | GET | Array[[Event](#event)]

### WebHook ID Endpoint

```shell
curl -s https://api.blockcypher.com/v1/eth/main/hooks/ce83d08f-7b86-4a6a-9b41-85507648f9c7?token=YOURTOKEN
{
  "id": "ce83d08f-7b86-4a6a-9b41-85507648f9c7",
  "token": "YOURTOKEN",
  "url": "https://my.domain.com/callbacks/new-tx",
  "callback_errors": 0,
  "address": "c5beef03aaa9548210b5550a62f7756efaa08ba3",
  "event": "unconfirmed-tx",
  "filter": "addr=c5beef03aaa9548210b5550a62f7756efaa08ba3\u0026event=unconfirmed-tx"
}
```

This resource returns an [Event](#event) based on its generated *id*.

Resource | Method | Return Object
-------- | ------ | -------------
/hooks/$WEBHOOKID | GET | [Event](#event)

WEBHOOKID is a string representing the event's generated *id*, for example:

`ce83d08f-7b86-4a6a-9b41-85507648f9c7`

### Delete WebHook Endpoint

```shell
# Piping into grep to get status code
curl -X DELETE -Is https://api.blockcypher.com/v1/eth/main/hooks/ce83d08f-7b86-4a6a-9b41-85507648f9c7?token=YOURTOKEN | grep "HTTP/1.1"
HTTP/1.1 204 No Content
```

This resource deletes an active [Event](#event) based on its *id*. Remember to include your token, or the request will fail.

Resource | Method | Return Object
-------- | ------ | -------------
/hooks/$WEBHOOKID | DELETE | *nil*

WEBHOOKID is a string representing the event's generated *id*, for example:

`ce83d08f-7b86-4a6a-9b41-85507648f9c7`

If successful, it won't return any objects, but will respond with an HTTP Status Code 204.
