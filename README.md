# Official Documentation for Exscudo public REST and Stream APIs

Exscudo API allows our users to receive market data as well as access and control their accounts and orders.


Document | Description
--- | ---
[rest-api-doc.md](./rest-api-doc.md) | Description and usage of REST endpoints
[ws-api-doc.md ](./ws-api-doc.md)    | Description and usage of WebSocket streams
[errors.md](./errors.md) | Possible error codes

### GENERAL

* The base endpoint for testnet is: `https://test.exscudo.com/api/v1/` for mainnet change the domain accordingly
* Port 443
* All endpoints return a JSON object
* For GET endpoints, parameters must be sent as a query string
* For POST endpoints, the parameters must be sent in the request body with content type "application/json”

### SECURITY

All API methods except REST Market data endpoints require authentication as described below.
Market data endpoints can be accessed with or without authentication. However request rate for unauthenticated calls is lower than for authenticated calls.
See more info in limits section of this manual.

* API key and Secret key can be generated in the settings section of user account
* API key and Secret key are case-sensitive.
* Signature should have UTF-8 encoding
* Secret key should not be included in the request and is only used to encode signature


Each request must contain an API key and a timestamp as query parameters.

Each request must contain the following headers:

* `X-EXC-APIKEY` - API key.
* `X-EXC-TIME` - a millisecond timestamp of when the request was created.
* `X-EXC-SIGN` - API Signature
* `X-EXC-PASS` - Passphrase known only to user

Both API Key and Timestamp should be the same as those passed via query parameters.

Signature is generated as follows:

For GET requests `Request URI with timestamp and API key as query parameters` encoded by secret key with HmacSHA256.
For POST requests a json body payload must be included after URI and query parameters.

The method name should be in the UPPER CASE.

#### Examples:

A request can be as following (in this example the request is GET and has no payload):

curl -H 'X-EXC-APIKEY:123' -H 'X-EXC-TIME:1529591374427' -H ‘X-EXC-PASS: USER_PASS_PHRASE’ -H 'X-EXC-SIGN:8227d67150f232b1e79d4878198b3d80d2c4464709ee6f21b46ddb6e9a866581' \
-X GET "https://test.exscudo.com:443/api/v1/ping/?apikey=123&timestamp=1529591374427"

To generate the X-EXC-SIGN header of an API signature the user must encode with Secret key by HmacSHA256:

for GET requests - an URI and all query parameters for example = /api/v1/markets?apikey=qweqwe&timestamp=1234566789

for POST requests - an URI, query parameters and request body (if exists) exactly in the order as described in the following example::

/api/v1/order/?apikey=123&timestamp=1535451317896{"accountId":976109372000000,"operation":"BUY","symbol":"BTCEON","type":"OPEN_LIMIT","amount":10,"limitPrice":10,"stopPrice":1,"terminalId":"qweqwe","clientOrderId":132,"orderPeriod":"DAY"}

Encoding can be made with variety of on-line tools, for example https://www.freeformatter.com/hmac-generator.html.

Please see curl on-line documentation for additional info on request syntax.

### LIMITS

* If an IP address exceeds the allowed amount of requests per minute (default 60 for accessing endpoints with authentication and 5 if accessing market endpoints without authentication)
to a specific REST API endpoint - a HTTP 1003 code will be returned with error response.
* Upon receiving this response requests should be ceased.
* Repeated API spamming will result in IP address ban.

### DEPTH

Depth parameter represents user data snapshot depth in seconds.
For example if depth is set to 5 and user subscribes to his Order updates or calls REST method for receiving his history trades, he will first receive a snapshot for the last 5 seconds.
By default depth = X seconds and user doesn't need to explicitly set depth unless there is a need for some specific value.

Depth is set when any of the following actions is made:

- User calls REST /init method and sets depth explicitly as described in /init method
- User subscribes to WebSocket feed and sets depth query parameter as described in WebSocket API section of this document.
- User calls any other REST method or subscribes to WebSocket feed without setting depth query parameter - then default depth value will be applied.

Snapshot depth is set only once per session and cannot be changed while the connection to API is active.
By default connection is terminated if no websocket connections are present and no REST requests have been made within the last minute.
It can also be explicitly terminated with /deinit call.

Depth is measured in seconds i.e. if depth = 60 then snapshots will be provided for the last minute.
Max and default depth value = 24 hours.
If depth parameter is not passed or depth = 0 then default value is applied.
If depth = 1 then no snapshots will be provided