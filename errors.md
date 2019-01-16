## Error codes for Exscudo API

All endpoints can return errors; the error payload is as follows:
```json
{
   "success": false,
   "errorCode" : 42,
   "error": "Text of error"
}
```

#### General server and auth errors

|Error code|Description|
|---|---|
|10001|Failed to authorize. Server error|
|10002|Server error. Unable to process request. Please report to tech support|
|10003|Too many requests|
|10004|Authentication failed. Auth headers missing|
|10005|Authentication failed. Incorrect passphrase or api key|
|10006|Authentication failed. Invalid URL|
|10007|Authentication failed. Invalid signature|
|10008|Authentication failed. Old request|


#### REST endpoint errors

|Error code|Description|
|---|---|
|10100|Market with requested code not found|
|10101|Requested symbol not found|
|10102|Incorrect depth|
|10103|Failed to get snapshot for requested symbol|
|10104|Incorrect trades count|
|10105|Incorrect interval|
|10106|Incorrect filtering parameters|
|10107|Required request body is missing|
|10108|Required parameters are missing|
|10109|Failed to place order|
|10110|Failed to cancel order|
|10111|Requested order not found|

#### Order processing errors

These are the most common order processing errors. The list is not final and may be updated in future.
 
|Error code|Description|
|---|---|
|1001|Has no enough funds|
|1002|Short operation isn't allowed for portfolio|
|1007|Order with ID doesn't exist|
|1008|Order with ID already inactive (possibly filled)|
|1011|Order could be already sent to exchange, but exchange identifier wasn't yet assigned - make another try later|
|1017|Price level have to be positive number less than N|
|1018|Amount have to be positive number not greater than volume N|