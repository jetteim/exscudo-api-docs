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
|1001|Failed to authorize. Server error|
|1002|Server error. Unable to process request. Please report to tech support|
|1003|Too many requests|
|1004|Authentication failed. Auth headers missing|
|1005|Authentication failed. Incorrect passphrase or api key|
|1006|Authentication failed. Invalid URL|
|1007|Authentication failed. Invalid signature|
|1008|Authentication failed. Old request|


#### REST endpoint errors

|Error code|Description|
|---|---|
|1100|Market with requested code not found|
|1101|Requested symbol not found|
|1102|Incorrect depth|
|1103|Failed to get snapshot for requested symbol|
|1104|Incorrect trades count|
|1105|Incorrect interval|
|1106|Incorrect filtering parameters|
|1107|Required request body is missing|
|1108|Required parameters are missing|
|1109|Failed to place order|
|1110|Failed to cancel order|
|1111|Requested order not found|