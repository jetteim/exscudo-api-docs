## WebSocket API
These methods require authentication as described in Readme.

To open a websocket connection subscribe to the following endpont URI.

For testnet:

```url
wss://test.exscudo.com/api/v1/socket
```

For mainnet change the domain accordingly.

Port 443.

* Messages sent and received via websocket channels are encoded in JSON format
* All requests must contain API key and timestamp in headers and API key, signature and timestamp  as query params. For more info see Security section of README.
* A request to WebSocket should contain parameters:

**type** - available values:  `SUBSCRIBE`, `UNSUBSCRIBE`

**feed** - available values:

`ORDER_BOOK` - order book

`QUOTES` - currency pairs quotes

`TRADES` - trade history

`TICKER`- candles updates

**symbols** - a list of currency pairs to subscribe to

**marketCode** - a unique market code


An optional query parameter `depth` can be added to the url to indicate required depth of snapshot of user data subscriptions. See Depth section of README for more info.

Url with depth example:

```url
wss://test.exscudo.com/api/v1/socket?depth=100
```

An optional parameter `cod`  meaning cancel on disconnect can be added to the url to indicate id the user wishes to cancel all active orders upon intentional or unintentional session disconnect.

Url with cod example:

```url
wss://test.exscudo.com/api/v1/socket?cod=yes
```

If `cod` parameter is not included in URI then default value is to not cancel orders.

| To avoid server connection disruptions, Websocket-message of the PING type must be sent not less than once every 5 minutes. |
| ------------------------------------------------------------ |
|                                                              |

### STREAMS

Upon subscribing to a channel an initial snapshot is sent with depth equal to one initiated upon subscription.

#### Order book

Subscription to order book example:

```json
{
  "id": 42,
  "type": "SUBSCRIBE",
  "feed": "ORDER_BOOK",
  "symbols": ["EONBTC"],
  "marketCode": "XCCX"
}
```

Id can be any of subscriber's choice and is used to determine to which subscriber to send a response to.

The order book stream will push messages with the following payload:

```json
{
  "type": "ORDER_BOOK",
  "symbol": "EONBTC",
  "timestamp":1528005164546,
  "snapshot": true,
  "bids": [[1.0,4.0],[3.0,17.0],[8.0,3.0],[9.0,16.0],[11.0,5.0],[27.0,17.0],[28.0,6.0],[30.0,3.0],[31.0,11.0],[33.0,6.0],[35.0,18.0],[36.0,3.0],[58.0,15.0],[61.0,18.0],[64.0,3.0],[69.0,17.0],[76.0,7.0],[80.0,10.0],[88.0,12.0],[89.0,18.0],[95.0,1.0]],
  "asks": [[13.0,3.0],[18.0,13.0],[19.0,12.0],[21.0,19.0],[26.0,8.0],[28.0,11.0],[32.0,12.0],[35.0,3.0],[37.0,18.0],[53.0,8.0],[60.0,8.0],[64.0,4.0],[71.0,15.0],[74.0,10.0],[96.0,15.0],[99.0,3.0],[100.0,11.0]]
}
```
#### Currency pairs quotes

Subscription to currency pairs quotes:

```json
{
  "id": 42,
  "type": "SUBSCRIBE",
  "feed": "QUOTES",
  "symbols": ["EONBTC"],
  "marketCode": "XCCX"
}
```

Messages delivered by the quotes stream contain information about requested currency pairs:

```json
{
  "type": "QUOTES",
  "symbol": "EONBTC",
  "bestAsk": 149.0,
  "bestBid": 149.0,
  "askLotsTotal": 12345,
  "bidLotsTotal": 12345,
  "lastPrice": 14.0,
  "totalLotsDay": 135.0,
  "dailyPriceChange": 1.0,
  "rolling24HoursVolume": 5.23,
  "timestamp": 1528005164546,
  "snapshot": true
}
```

`available` - a symbol availability indicator flag.
`dailyPriceChange` - price change from 00:01 AM measured in percents

#### Trade history

Subscription to trade history stream:

```json
{
  "id": 417,
  "type": "SUBSCRIBE",
  "feed": "TRADES",
  "symbols": ["EONBTC"],
  "marketCode": "XCCX"
}
```

Returns response as follows:

```json
{
  "timestamp": 1528005164546,
  "tid": 123,
  "price": 85050.896000,
  "amount": 0.38026198,
  "market": "XCCX",
  "symbol": "BTCEON",
  "operation": "BUY",
  "type": "TRADES"
}
```


#### Ticker

```json
{
  "id": 417,
  "type": "SUBSCRIBE",
  "feed": "TICKER",
  "symbols": ["EONBTC"],
  "marketCode": "XCCX",
  "interval": "ONE_MINUTE"
}
```
Available interval values:

INSTANT, ONE_MINUTE, FIVE_MINUTES, TEN_MINUTES, FIFTEEN_MINUTES, THIRTY_MINUTES, ONE_HOUR, TWO_HOURS,
FOUR_HOURS, ONE_DAY, ONE_WEEK, ONE_MONTH, ONE_QUARTER, ONE_YEAR;

Response stream will push messages with payload as follows:

```json
{
  "type": "TICKER",
  "interval": "ONE_MINUTE",
  "snapshot": true,
  "points": [{
    "date": 1519865460000,
    "open": 0.11645000,
    "close": 0.11699000,
    "high": 0.11699000,
    "low": 0.11645000,
    "volume": 0.00,
    "state": "OPEN"
  }, {
    "date": 1519865520000,
    "open": 0.11599000,
    "close": 0.11599000,
    "high": 0.11676000,
    "low": 0.11576000,
    "volume": 0,
    "state": "OPEN"
  }]
 }
```
Update messages are as follows:
```json
{
  "type": "TICKER",
  "interval": "ONE_MINUTE",
  "snapshot": false,
  "points": [{
    "symbol": "EONBTC",
    "bid": 244.75,
    "ask": 244.76,
    "openPrice": 344.29323929169084,
    "lastPrice": 244.82,
    "low": 244.2,
    "high": 248.19,
    "volume": 7842.11542563,
    "timestamp": 7898784551444253
  }]
}
```

#### User Account data

Request:

```json
{
  "id": 42,
  "type": "SUBSCRIBE_USER_DATA",
  "symbols": ["BTCEON"],
}
```
**accountIds** - should contain ids of accounts for each symbol in a currency pair
**type** - SUBSCRIBE_USER_DATA, UNSUBSCRIBE_USER_DATA

This stream pushes account, order and deal change events as follows:

```json
{
 "type": "ACCOUNT",
 "items": [{
    "id": 42,
    "type": "TRADE",
    "currency": "BTC",
    "name": "myacc",
    "code": "BTC-001",
    "totalAmount": 0.000000,
    "availableAmount": 0.000000
  }]
}
```

```json
{
  "type": "ORDER",
  "items": [{
    "terminalId": "robot-1",
    "clientOrderId": 1242,
    "orderId": 448364249,
    "accountId": 32,
    "symbol": "BTCUSD",
    "market": "XCCX",
    "price": 0.01,
    "operation": "B",
    "type": "OPEN_LIMIT",
    "createdTime": 1524839435493,
    "updatedTime": 1524839435493,
    "status": "CREATED",
    "isActive": true,
    "isSuccessfullyPlaced": true,
    "remainingAmount": 0.01,
    "lifePeriod": "day",
    "executedAmount": 0,
    "totalAmount": 1.00000000,
    "priceLimit": 1.00000000,
    "comment": "error text if unsuccessful"
  }]
}
```

```json
{
  "type": "DEAL",
  "items": [{
    "tradeNo": 1,
    "orderId": 2,
    "accountId": 3,
    "lotsLeft": 4,
    "price": 0.000000,
    "sum": 0.000000,
    "volume": 0.000000,
    "name": "blah",
    "priceDecimals": 1,
    "volumeDecimals": 2,
    "symbol": "BTCEON",
    "operation": "B"
  }]
}
```