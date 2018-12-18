## REST API

### RESPONSE FORMAT

Successful response contains json payload as follows:

```json
{
  "success": true,
  "payload": {
    "field1": "value1",
    "field2": "value2"
  }
}
```

All endpoints can return errors; the error payload is as follows:

```json
{
  "success": false,
  "errorCode": 429,
  "error": "Text of error"
}
```

### ENDPOINTS

##### `! Optional parameters are given in request URI after "?" sign.`

##### `! In the requests specs below only the method part of your URI is stated. Please see the "General" section for the base endpoint.`

### Support methods
These methods require authentication as described in Readme.

#### Init

`GET /init/{depth}`

Used to explicitly set snapshot depth for all further user data requests. See depth section of README for more info.

#### Deinit

`GET /deinit`

Used to explicitly terminate all user's interaction with API server.
Deinit closes all user's active orders and terminates all websocket connections. It also erases snapshot 'depth' value used in current session.

If successful returns:

```json
{
  "success": true,
}
```

### Market data
These methods can be accessed both with or without authentication.

#### Markets

`GET /markets`

Returns a list of available markets:

```json
{
  "success": true,
  "payload": [
    {
      "name": "Crypto",
      "code": "XCCX"
    },
    {
      "name": "Test Market",
      "code": "TEST"
    }
  ]
}
```

#### Symbols

`GET /markets/symbols`

Query parameters:

| Parameter (optional) | Type   | Comment                                                      |
| -------------------- | ------ | ------------------------------------------------------------ |
| market               | String | optional. Market code. If not passed then default XCCX market will be used. |

Returns a list of available symbols that can be used in requests:

```json
{
  "success": true,
  "payload": {
      "market" : "XCCX",
      "symbols" : ["EONBTC", "BTCLTC"]
  }
}
```

#### Symbol details

`GET /symbols/details`

Query parameters:

| Parameter (optional) | Type   | Comment                                                      |
| -------------------- | ------ | ------------------------------------------------------------ |
| market               | String | optional. Market code. If not passed then default XCCX market will be used. |

Returns details for all available symbols:

```json
{
  "success": true,
  "payload": [{
      "symbol": "ETHEON",
      "pricePrecision": 6,
      "amountPrecision": 8,
      "maximumOrderSize": 1000.00000000,
      "minimumOrderSize": 0.01000000,
      "minimumOrderPrice": 1.000000,
      "maximumOrderPrice": 1000000.000000,
      "tradingFlag": "LIMIT_CANCEL"
    },
    {
      "symbol": "ETCEON",
      "pricePrecision": 6,
      "amountPrecision": 8,
      "maximumOrderSize": 10000.00000000,
      "minimumOrderSize": 0.10000000,
      "minimumOrderPrice": 0.100000,
      "maximumOrderPrice": 1000000.000000,
      "tradingFlag": "LIMIT_CANCEL"
    },
    {
      "symbol": "BCHEON",
      "pricePrecision": 6,
      "amountPrecision": 8,
      "maximumOrderSize": 100.00000000,
      "minimumOrderSize": 0.00100000,
      "minimumOrderPrice": 10.000000,
      "maximumOrderPrice": 1000000.000000,
      "tradingFlag": "LIMIT_CANCEL"
    }
  ]
}
```

| Parameter           | Comment                                                      |
| ------------------- | ------------------------------------------------------------ |
| maximumOrderSize    | may be empty                                                 |
| pricePrecision      | number of symbols after comma for USD price (in BTCUSD symbol) |
| amountPrecision     | number of symbols after comma for BTC amount (in BTCUSD symbol as example) |
| trading flag        | symbol availability                                          |
| Available values for trading flag:   |                                                              |
| MARKET_LIMIT_CANCEL | all operations including order market placement are allowed right now for this symbol |
| MARKET_CANCEL       | order cancellation and market placement are currently allowed for this symbol |
| LIMIT_CANCEL        | order cancellation and limit placement are currently allowed for this symbol |
| CANCEL              | only order cancellation is currently allowed for this symbol |
| NONE                | no operations are currently allowed for this symbol          |
| BLOCKED             | the symbol is blocked by server                              |
| EXPIRED             | the symbol will never be traded anymore                      |
| DELETED             | the symbol was deleted from the exchange                     |
| OBSOLETE            | the symbol is obsolete. All partially completed periods are flushed |

#### Tickers for symbol

`GET /tickers/{symbol}`

Query parameters:

| Parameter (optional) | Type   | Comment                                                      |
| -------------------- | ------ | ------------------------------------------------------------ |
| market               | String | optional. Market code. If not passed then default XCCX market will be used. |

Returns payload for the requested symbol:

```json
{
  "success": true,
  "payload": {
    "symbol": "EONBTC",
    "bid": 244.75,
    "ask": 244.76,
    "openPrice": 344.29323929169084,
    "lastPrice": 244.82,
    "low": 244.2,
    "high": 248.19,
    "volume": 7842.11542563,
    "timestamp": 7898784551444
  }
}
```

**lastTrade** - timestamp in millis

#### Tickers

`GET /tickers`

Query parameters:

| Parameter (optional) | Type   | Comment                                                      |
| -------------------- | ------ | ------------------------------------------------------------ |
| market               | String | optional. Market code. If not passed then default XCCX market will be used. |

Overview of the state of the market

```json
{
  "success": true,
  "payload": [{
    "symbol": "EONBTC",
    "bid": 244.75,
    "ask": 244.76,
    "openPrice": 344.29323929169084,
    "lastPrice": 244.82,
    "low": 244.2,
    "high": 248.19,
    "volume": 7842.11542563,
    "timestamp": 7898784551444253
  }, {
    "symbol": "LTCBTC",
    "bid": 244.75,
    "ask": 244.76,
    "lastPrice": 244.82,
    "low": 244.2,
    "high": 248.19,
    "volume": 7842.11542563,
    "timestamp": 7898784551444253
  }]
}
```

#### Order book

`GET /orderbook/{symbol}?depth={depth}&market={market}`

Query parameters:

| Parameter (optional) | Type   | Comment                                                      |
| -------------------- | ------ | ------------------------------------------------------------ |
| market               | String | optional. Market code. If not passed then default XCCX market will be used. |
| depth                | Int    | limits the number of bids and asks returned. Allowed values: 1 - 30. Default value is 5. If passed depth = 0 then full orderbook is returned |

Returns the state of the order book for requested symbol:

```json
{
  "success": true,
  "payload": {
    "bids": [{
      "price": 574.61,
      "amount": 0.1439327
    }],
    "asks": [{
      "price": 574.62,
      "amount": 19.1334
    }]
  }
}
```

Asks are listed in asc order
Bids in desc order

#### Trades

`GET /trades/{symbol}?fromEnd={timestamp}&tillEnd={timestamp}&count={count}`

| Parameter (optional) | Type    | Comment                                                      |
| -------------------- | ------- | ------------------------------------------------------------ |
| market               | String  | optional. Market code. If not passed then default XCCX market will be used. |
| count                | Int     | number of trades. IMPORTANT: In order to restrict heavy loads on a server there is a hard limit of 1000 trades per query |
| fromEnd              | Numeric | timestamp. Get all orders between fromEnd and tillEnd (if passed) or current time  |
| tillEnd              | Numeric | timestamp. Get all orders between fromEnd and tillEnd or default count of orders ending at tillEnd|
| count                | Numeric | If fromEnd and tillEnd not passed - get *count* orders from current time. If fromEnd and tillEnd passed - return max(*count*, [fromEnd, tillEnd]) |

Timestamps are in milliseconds from Unix epoch.

Use this link <https://currentmillis.com/> to convert date to milliseconds for experiments.

Returns a list of the most recent trades for the given symbol:

```json
{
  "success": true,
  "payload": [{
    "timestamp": 1519865460000,
    "tid": 11988919,
    "price": 244.8,
    "amount": 0.03297384,
    "market": "XCCX",
    "symbol": "BTC",
    "type": "sell"
  }]
}
```

#### Candles

`GET /candles/{symbol}?fromEnd={timestamp}&tillEnd={timestamp}&count={count}`

| Parameter (mandatory)    | Type     | Comment                                                      |
| ------------------------ | -------- | ------------------------------------------------------------ |
| market                   | String   | optional. Market code. If not passed then default XCCX market will be used. |
| symbol                   | String   | example: "BTCEON"                                            |
| **Parameter (optional)** | **Type** | **Comment**                                                  |
| fromEnd                  | Numeric  | timestamp. Get all orders between fromEnd and tillEnd (if passed) or current time  |
| tillEnd                  | Numeric  | timestamp. Get all orders between fromEnd and tillEnd or default count of orders ending at tillEnd|
| count                    | Integer  | If fromEnd and tillEnd not passed - get *count* orders from current time. If fromEnd and tillEnd passed - return max(*count*, [fromEnd, tillEnd]) |
| interval                 | String   | candle interval. Available values: ONE_MINUTE, FIVE_MINUTES, TEN_MINUTES, FIFTEEN_MINUTES, THIRTY_MINUTES, ONE_HOUR, TWO_HOURS, FOUR_HOURS, ONE_DAY, ONE_WEEK, ONE_MONTH, ONE_QUARTER, ONE_YEAR
Default: ONE_MINUTE |

Timestamps are in milliseconds from Unix epoch.

Use this link <https://currentmillis.com/> to convert date to milliseconds for experiments.

Returns candlestick bars for a requested symbol in a requested market:

```json
{
  "success": true,
  "payload": [{
    "startTime": 1519865460000,
    "endTime": 1519865460000,
    "open": 0.11645000,
    "close": 0.11699000,
    "high": 0.11699000,
    "low": 0.11645000,
    "volume": 0.00,
    "state": "OPEN"
  }, {
    "startTime": 1519865520000,
    "endTime": 1519865520000,
    "open": 0.11599000,
    "close": 0.11599000,
    "high": 0.11676000,
    "low": 0.11576000,
    "volume": 0.00,
    "state": "CLOSED"
  }]
}
```

**state** - OPEN, CLOSED, TIMEDOUT

### User data
These methods require authentication as described in Readme.

#### Accounts

`GET /accounts`

Returns all user's accounts:

```json
{
  "success": true,
  "payload": [{
    "id": 1,
    "type": "T",
    "currency": "BTC",
    "name": "My sweaty bloody money",
    "code": "BTC-001",
    "totalAmount": 1.488,
    "availableAmount": 1.337
  }, {
     "id": 2,
     "type": "D",
     "currency": "EON",
     "name": "Best ever",
     "code": "EON-001",
     "totalAmount": 13.37,
     "availableAmount": 1.488
   }]
}
```

| Field    | Type | Comment                                |
| -------- | ---- | -------------------------------------- |
| type     | Text | Account type: "T"- TRADE, "D"- DEPOSIT |
| currency | Text | Account currency                       |

#### User trade history

`GET /trades`

Filtering query parameters:

| Parameter (optional) | Type   | Comment                                                      |
| -------------------- | ------ | ------------------------------------------------------------ |
| market               | String | Market code. If not passed then default XCCX market will be used |
| orderId              | String | returns only trades linked to requested order                |
| symbol               | String | returns only trades for requested order                      |

Returns a list of trades according to requested filtering:

```json
{
"success": true,
  "payload": [{
    "timestamp": 1444266681,
    "tid": 11988919,
    "price": 244.8,
    "amount": 0.03297384,
    "market": "XCCX",
    "symbol": "BTC",
    "type": "sell"
  }]
}
```

#### New order

To submit a new order send request:

`POST /order`

Optional query parameters:

| Parameter (optional) | Type   | Comment                                                      |
| -------------------- | ------ | ------------------------------------------------------------ |
| market               | String | Market code. If not passed then default XCCX market will be used |
| terminalId           | String | an optional user-defined constant value used to determine request origin upon receiving a response |
| clientOrderId        | Int    | an optional user-defined unique value used to determine request origin upon receiving a response, must be integer number |
| type                 | String | supported values: OPEN_MARKET, OPEN_LIMIT, STOP_MARKET, STOP_LIMIT, ON_CLOSE |
| operation            | String | supported values: BUY, SELL                                  |
| orderPeriod          | String | supported values: `DAY` - Day , `GTC` - Good till canceled. If order period is not passed with request, the default value will be GTC |

Request to submit a new order:

```json
{
  "operation": "BUY",
  "symbol": "BTCEON",
  "type": "OPEN_MARKET",
  "amount": 1.00,
  "limitPrice": 1.02345,
  "stopPrice": 1.02345,
  "terminalId": "robot-1",
  "clientOrderId": 1242,
  "orderPeriod": "DAY"
}
```

Response payload will be as follows:

```json
{
  "success" : true,
  "payload" : {
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
  }
}
```

| Parameter     | Type | Comment                                                      |
| ------------- | ---- | ------------------------------------------------------------ |
| operation     | Text | Available operations: **B** - buy, **S** - sell              |
| status        | Text | CREATED, AWAITING, PENDING_PLACING, PENDING_MOVING, PENDING_SHIFTING, PENDING_CANCELLING,  PENDING_RECHARGING, SUBMITTED_PLACING, SUBMITTED_MOVING, SUBMITTED_SHIFTING, SUBMITTED_CANCELLING, DEACTIVATED,  REJECTED_BY_BROKER, REJECTED_PLACING, REJECTED_MOVING, REJECTED_BY_SYSTEM, ACTIVE, PARTIALLY, FULFILLED,  CANCELLED_BY_BROKER, CANCELLED_BY_SYSTEM, REMOVED_BY_SYSTEM, REMOVED_BY_CONTRAGENT |
| terminalId    | Text | same value as was passed in request                          |
| clientOrderId | Text | same value as was passed in request                          |

**ACTIVE** - Order placed successfully.

or

**REJECTED** - if something went wrong

#### Cancel order

To cancel an order the following request should be made:

`POST /order/cancel`

| Parameter (optional) | Type   | Comment                                                      |
| -------------------- | ------ | ------------------------------------------------------------ |
| market               | String | Market code. If not passed then default XCCX market will be used. |
| symbol               | String | returns only trades for requested order                      |

with request body:

```json
{
  "orderId": 1
}
```

If success response payload will be as follows:

```json
{
  "success": true,
  "payload": {
    "terminalId": null,
    "clientOrderId": null,
    "orderId": 1,
    "accountId": 1,
    "symbol": "BTCEON",
    "market": "XCCX",
    "price": null,
    "operation": "BUY",
    "type": "OPEN_LIMIT",
    "createdTime": 1532347566587,
    "updatedTime": 1532347566587,
    "status": "CANCELLED_BY_BROKER",
    "isActive": false,
    "isSuccessfullyPlaced": true,
    "remainingAmount": 0.02000,
    "lifePeriod": "DAY",
    "executedAmount": 0.00000,
    "totalAmount": 0.02000,
    "priceLimit": 1,
    "comment": null,
  }
}
```

#### Cancel all orders

`POST /order/cancel/all`

| Parameter (optional) | Type   | Comment                                                      |
| -------------------- | ------ | ------------------------------------------------------------ |
| market               | String | Market code. If not passed then default XCCX market will be used. |

If success response payload will be as follows:

```json
{
    "success": true
}
```

#### Order info

`GET /order/{orderId}`

```json
{
"success": true,
  "payload": {
    "terminalId" : "robot-1",
    "clientOrderId" : 1242,
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
  }
}
```

#### Open orders

`GET /orders/open`

| Parameter (optional) | Type   | Comment                                                      |
| -------------------- | ------ | ------------------------------------------------------------ |
| market               | String | Market code. If not passed then default XCCX market will be used. |
| symbol               | String | optional query parameter if payload should be narrowed to requested symbol |

To get all open orders for the requested account.

Returns the following payload:

```json
{
  "success": true,
  "payload": [{
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

#### Closed orders

`GET https://test.exscudo.com/api/v1/orders/closed`

To get all closed orders for the requested account.

| Parameter (optional) | Type   | Comment                                                      |
| -------------------- | ------ | ------------------------------------------------------------ |
| market               | String | Market code. If not passed then default XCCX market will be used. |
| symbol               | String | optional query parameter if payload should be narrowed to requested symbol |

Returns same payload as in Open orders request with additional field in payload:

```json
{
  "comment": "text of error description if closed because of error"
}
```