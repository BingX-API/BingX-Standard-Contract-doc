<!-- TOC -->

- [Basic Information](#basic-information)
  - [Service Address](#service-address)
  - [Service Application](#service-application)
  - [Common Error Codes](#common-error-codes)
- [Signature Authentication](#signature-authentication)
  - [Signature Description](#signature-description)
- [Standard contract interface](#standard-contract-interface)
    - [Position](#position)
    - [Order history](#order-history)
    - [Query standard contract balance](#Query-standard-contract-balance)

<!-- TOC -->

# Basic Information
## Service Address

https://open-api.bingx.com

HTTP 200 status code indicates a successful response. The response body might contain a message which will be displayed accordingly.

## Service Application

The API is currently in internal testing, and the application page will be opened soon, please be patient. If you have other needs, please contact customer service.

## Common Error Codes

**Common HTTP Error Codes**

* 4XX error codes are used to indicate wrong request content, behavior, format

* 5XX error codes are used to indicate problems on the server's side

**Common Business Error Codes**

* 100001 - Signature authentication failed

* 100202 - Insufficient balance

* 100400 - Invalid parameter

* 100440 - Order price deviates greatly from the market price

* 100500 - Internal server error

* 100503 - Server busy

**Notes**:
* If it fails, there will be an error description included in the response body

* Each interface may throw an exception

# Signature Authentication
Interfaces that require authentication must contain following information:

* Pass `apiKey` with the `X-BX-APIKEY` in the request header.
* Request parameters include `signature` that is computed based on signature algorithm.
* Request parameters include `timestamp` as the timestamp of the request，the unit is millisecond. When the server receives the request, it will judge the timestamp in the request. The request will be considered invalid if it was sent 5000 milliseconds ago. This window time value can be defined by sending the optional parameter `recvWindow`.

## Signature Description
`signature` is created by using **HMAC SHA256** algorithm to encrypt the request parameters.

**Example: Sign the following request**
- API parameters:
```
quoteOrderQty = 20
side = BUY
symbol = ETH-USDT
timestamp = 1649404670162
type = MARKET
```
- API information:
```
apiKey = Zsm4DcrHBTewmVaElrdwA67PmivPv6VDK6JAkiECZ9QfcUnmn67qjCOgvRuZVOzU
secretKey = UuGuyEGt6ZEkpUObCYCmIfh0elYsZVh80jlYwpJuRZEw70t6vomMH7Sjmf94ztSI
```
- Parameters are sent via `query string` example
```
1. Assemble API parameters: quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET
2. Use secretKey to generate a signature from the assembled parameter string: 428a3c383bde514baff0d10d3c20e5adfaacaf799e324546dafe5ccc480dd827
   echo -n "quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET" | openssl dgst -sha256 -hmac "UuGuyEGt6ZEkpUObCYCmIfh0elYsZVh80jlYwpJuRZEw70t6vomMH7Sjmf94ztSI" -hex
3. Send request: curl -H 'X-BX-APIKEY: Zsm4DcrHBTewmVaElrdwA67PmivPv6VDK6JAkiECZ9QfcUnmn67qjCOgvRuZVOzU' 'https://open-api.bingx.com/openApi/spot/v1/trade/order?quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET&signature=428a3c383bde514baff0d10d3c20e5adfaacaf799e324546dafe5ccc480dd827'
```
- Parameters are sent via `request body` example
```
1. Assemble API parameters: quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET
2. Use secretKey to generate a signature from the assembled parameter string: 428a3c383bde514baff0d10d3c20e5adfaacaf799e324546dafe5ccc480dd827
   echo -n "quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET" | openssl dgst -sha256 -hmac "UuGuyEGt6ZEkpUObCYCmIfh0elYsZVh80jlYwpJuRZEw70t6vomMH7Sjmf94ztSI" -hex
3. Send request: curl -H 'X-BX-APIKEY: Zsm4DcrHBTewmVaElrdwA67PmivPv6VDK6JAkiECZ9QfcUnmn67qjCOgvRuZVOzU' -X POST 'https://open-api.bingx.com/openApi/spot/v1/trade/order' -d 'quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET&signature=428a3c383bde514baff0d10d3c20e5adfaacaf799e324546dafe5ccc480dd827'
```
- Parameters are sent via `query string` and `request body` example
```
queryString: quoteOrderQty=20&side=BUY&symbol=ETHUSDT
requestBody: timestamp=1649404670162&type=MARKET

1. Assemble API parameters: quoteOrderQty=20&side=BUY&symbol=ETHUSDTtimestamp=1649404670162&type=MARKET
2. Use secretKey to generate a signature from the assembled parameter string: 94e0b4925060a615e1e372d4c929015d4b59d3c89067dc0beeafcfb33a6d8d10
   echo -n "quoteOrderQty=20&side=BUY&symbol=ETHUSDTtimestamp=1649404670162&type=MARKET" | openssl dgst -sha256 -hmac "UuGuyEGt6ZEkpUObCYCmIfh0elYsZVh80jlYwpJuRZEw70t6vomMH7Sjmf94ztSI" -hex
3. Send request: curl -H 'X-BX-APIKEY: Zsm4DcrHBTewmVaElrdwA67PmivPv6VDK6JAkiECZ9QfcUnmn67qjCOgvRuZVOzU' -X POST 'https://open-api.bingx.com/openApi/spot/v1/trade/order?quoteOrderQty=20&side=BUY&symbol=ETHUSDT' -d 'timestamp=1649404670162&type=MARKET&signature=94e0b4925060a615e1e372d4c929015d4b59d3c89067dc0beeafcfb33a6d8d10'
```
# Standard contract interface

## Position

**API**
````
    GET /openApi/contract/v1/allPosition
````

**Parameters**

none

**Response**

| parameter name | type | remarks |
| ------ | ------ | ------ |
| symbol | string | symbol |
| initialMargin | number | Margin |
| leverage | number | leverage number |
| unrealizedProfit | number | Position unrealized profit and loss |
| isolated | bool | Whether it is isolated mode |
| entryPrice | number | Holding Cost Price |
| positionSide | number | position direction, LONG and SHORT |
| positionAmt | number | Transaction data|
| currentPrice | number | The current price. If there is no closing price, the current price will be returned |
| time | int64 | Position opening time |
example:

````
{
    "code": 0,
    "timestamp": 1666421703835,
    "data": [
        {
            "currentPrice": 19145.65,
            "symbol": "BTC/USDT",
            "initialMargin": 2,
            "unrealizedProfit": -0.7239062,
            "leverage": 1,
            "isolated": true,
            "entryPrice": 30006.65,
            "positionSide": "LONG",
            "positionAmt": 0.00006666,
            "time": 1654782192000
        }
    ]
}
````

## Order history

**API**
````
    GET /openApi/contract/v1/allOrders
````

**Parameters**

| parameter name | type | remarks |
| ------ | ------ | ------ |
| symbol | string | Currency pair, the format is similar: BTC-USDT, required |
| orderId | int64 | order ID, optional |
| startTime | int64 | start time, optional |
| endTime | int64 | end time, optional |
| limit | int64 | quantity, optional |

**Response**

| parameter name | type | remarks |
| ------ | ------ | ------ |
| avgPrice | number | Closing price |
| cumQuote | number | transaction amount |
| executedQty | number | volume |
| orderId | number | System order number |
| positionSide | string | Position direction, LONG and SHORT |
| status | string | order status CLOSED |
| symbol | string | Currency pair, the format is similar: BTC-USDT |
| time | int64 | order time |
| updateTime | int64 | update time |
| margin | number | margin |
| leverage | number | leverage number |
| isolated | bool | Whether it is isolated mode |
| closePrice | number | closing price |
| positionId | int64 | Position ID |

example:

````
{
    "code": 0,
    "timestamp": 1666421402448,
    "data": [
        {
            "margin": 2,
            "leverage": 1,
            "closePrice": 19138.3,
            "positionId": 1047766884761493500,
            "isolated": true,
            "avgPrice": 29212.91,
            "cumQuote": 2,
            "executedQty": 0.00006846,
            "orderId": 1047766884761493500,
            "positionSide": "SHORT",
            "status": "CLOSED",
            "symbol": "BTC-USDT",
            "time": 1653661587000,
            "updateTime": 1666421388000
        }
    ]
}
````


## Query standard contract balance

**interface**
```
    GET /openApi/contract/v1/balance
```

**parameter**

none

**response**

| parameter name                 | type     | Remark         |
| ------                |--------|------------|    
| asset                | string | assets         |
| balance         | string | total balance        |
| crossWalletBalance              | string | Cross position balance       |
| crossUnPnl      | string | Unrealized profit and loss of cross positions  |
| availableBalance              | string | Order available balance     |
| maxWithdrawAmount            | string | Maximum transferable balance    |
| marginAvailable          | bool   | Can it be used as a joint bond |
| updateTime           | number | timestamp        |

example：

```
{
    "code": 0,
    "timestamp": 1666421703835,
    "data": [
        {
            "asset": "USDT",        // assets
            "balance": "122607.35137903",   // total balance
            "crossWalletBalance": "23.72469206", // Cross position balance
            "crossUnPnl": "0.00000000"  // Unrealized profit and loss of cross positions
            "availableBalance": "23.72469206",       // Order available balance 
            "maxWithdrawAmount": "23.72469206",     // Maximum transferable balance
            "marginAvailable": true,    // Can it be used as a joint bond
            "updateTime": 1617939110373
        }
    ]
}
```


<!-- TOC -->

- [Websocket Introduction](#websocket-introduction)
    - [Access](#access)
    - [Data Compression](#data-compression)
    - [Heartbeats](#heartbeats)
    - [Subscriptions](#subscriptions)
    - [Unsubscribe](#unsubscribe)
- [Websocket Market Data](#websocket-market-data)
    - [Trade Streams](#1-trade-streams)
    - [Kline Streams](#2-kline-streams)
    - [Partial Book Depth Streams](#3-partial-book-depth-streams)
- [Websocket Account Data](#Websocket-account-data)
    - [Order Update Streams](#1-order-update-streams)
    - [Subscribe to account balance push](#2-Subscribe-to-account-balance-push)

<!-- /TOC -->

# Websocket Introduction

## Access

The base URL of Websocket Market Data is: `wss://open-api-ws.bingx.com/market`

## Data Compression

All response data from Websocket Server are compressed into GZIP format. Clients have to decompress them for further use.

## Heartbeats

Once the Websocket Client and Websocket Server get connected, the Server will send a heartbeat-Ping message every 5 seconds (the frequency might change).

When the Websocket Client receives this heartbeat message, it should return Pong message.

## Subscriptions

After successfully establishing a connection with the Websocket Server, the Websocket Client sends the following request to subscribe to a specific topic:
```
{
"id": "id1",
"dataType": "data to sub"
}
```
After a successful subscription, the Websocket Client will receive a confirmation message:
```
{
"id": "id1",
"code": 0,
"msg": ""
}
```
After that, once the subscribed data is updated, the Websocket Client will receive the update message pushed by the Server

| Parameters | Type  | Required | Description    |
| ------------- |----|----|----------------------|
| id | String | yes | id is the unique id passed in by the user, it will be returned when it is returned, which is used to distinguish idempotent verification by the user|


Error code description
```
    0:"SUCCESS"
   

    100204:"SEARCH_NO_CONTENT"

  
    100205:"REPEAT_REQUEST"

 
    100400:"ILLEGAL_ARGUMENT"

 
    100401:"AUTHENTICATION_FAIL"

   
    100403:"AUTHORIZATION_FAIL"

  
    100410:"FREQUENCY_LIMIT"

   
    100500:"INTERNAL_SERVER_ERROR"

   
    100503:"SERVER_BUSY"
```

## Unsubscribe

The format to unsubscribe is as follows:
```
{
"id": "id1",
"reqType": "unsub",
"dataType": "data to unsub"
}
```
Confirmation of Unsubscription:
```
{
"id": "id1",
"code": 0,
"msg": ""
}
```

# symbol Description

The symbol must all be capitalized

# Websocket Market Data

## 1. Trade Streams

    The feature pushes real-time information as a snapshot when a trade is made. Each transaction or trade has a unique buyer and seller.

**Subscription Type**

    dataType is <symbol>@trade, such as BTC-USDT@trade ETH_USDT@trade

**Subscription Example**

    {"id":"24dd0e35-56a4-4f7a-af8a-394c7060909c","dataType":"BTC-USDT@trade"}

**Subscription Parameters**


| Parameters | Type  | Required | Description    |
| ------------- |----|----|----------------------|
| symbol | String | YES | There must be a dash "-" in the trading pair symbol, e.g., BTC-USDT |


**Push Data**

| Return Parameters     | Field Description |  
|----------|---------------------------|
| dataType | The type of data subscribed, such as BTC-USDT@trade |
| data     | Push data			|
| e        | Event type			|
| E        | Event time			|
| s        | Symbol			|
| t        | Trade ID			| 
| p        | Price			| 
| q        | Quantity			| 
| T        | Trade time			|
| m        | Is the buyer the market maker?                      |

```javascript
  # Response
  {
      "data": {
            "E": 1649832413551,
            "T": 1649832413512,
            "e": "trade",
            "p": "40125.48",
            "q": "0.007146",
            "s": "BTC-USDT",
            "t": "33685717",
            "m": true
     },
     "dataType": "BTC-USDT@trade"
  }

```


## 2. Kline Streams

    The Kline Stream push updates to the (current) kline of requested type every second.

**Subscription Type**

    dataType is <symbol>@kline_<interval>, such as BTC-USDT@kline_1min

**Subscription Example**

    {"id":"e745cd6d-d0f6-4a70-8d5a-043e4c741b40","dataType":"BTC-USDT@kline_1min"}

** Subscription Parameters**

| Parameters  | Type  | Required | Field Description | Description                   |
| -------|--------|--- |-------|----------------------|
| symbol | String | YES |Trading pair | There must be a dash "-" in the contract name, e.g., BTC-USDT |
| interval | String | YES |Kline type| Parameter description, e.g., minute, hour, week, etc. kline type ,1min, 5min, 15min, 30min, 60min, 1day    |


**Remarks**

For now, only 1 min kline data is provided

| klineType  | Field Description| 
| ----------|----|
| 1min         | 1 min Kline |


**Push Data**

| Return Parameters     | Field Description                           |  
|----------|--------------------------------|
| dataType | The type of data subscribed, such as BTC-USDT@kline_1min |
| data     | Push data                           |
| e        | Event type                          |
| E        | Event time                          |
| s        | Symbol                            |
| K        | Kline data                       |
| t        | Kline start time                      |
| T        | Kline close time                      |
| s        | Symbol                            |
| i        | Interval                           |
| o        | Open price                   |
| c        | Close price                  |
| h        | High price                  |
| l        | Low price                   |
| v        | Base asset volume         |
| n        | Number of trades 	      |
| q        | Quote asset volume        |

   ```javascript
    # Response
{
  "data": {
  "E": 1649832726550,
          "K": {
           "T": 1649832779999,
            "c": "40017.48",
            "h": "40027.83",
            "i": "1min",
            "l": "40017.48",
            "n": 13,
            "o": "40025.42",
            "q": "2693.492344",
            "s": "BTC-USDT",
            "t": 1649832720000,
            "v": "0.067295"
      },
          "e": "kline",
          "s": "BTC-USDT"
},
    "dataType": "BTC-USDT@kline_1min"
}
   ```

## 3. Partial Book Depth Streams

    Partial book depth information is pushed every second, fixed at level 20. Some trading pairs may not reach level 20. 100 files can be selected

**Subscription Type**

       dataType is <symbol>@depth<level>，as BTC-USDT@depth, BTC-USDT@depth20, BTC-USDT@depth100 

```
 {"id":"975f7385-7f28-4ef1-93af-df01cb9ebb53","dataType":"BTC-USDT@depth"}
```
**Subscription Parameters**

| Parameters  | Type  | Required | Field Description | Description                   |
| -------|--------|--- |-------|----------------------|
| symbol | String | YES |Trading pair symbol| There must be a dash "-" in the trading pair, e.g., BTC-USDT |


**Push Data**

| Return Parameters         | Field Description                      |  
|--------------|---------------------------|
| dataType | The type of data subscribed, such as BTC-USDT@depth |
| data         | Push data                      |
| bids         | Bids to be updated                   |
| asks | Asks to be updated                   |



   ```javascript
    # Response
{
      "dataType": "BTC-USDT@depth",
      "data": {
        "bids": [
              [
                "43302.00", // Price level to be updated
                "0.000021"  // Quantity
              ]
        ],
          "asks": [
              [
                "43499.00", // Price level to be updated
                "0.000021"  // Quantity
              ]
          ]
       }
}
   ```

# Websocket Account Data

Note that websocket authentication is required to obtain such information, use listenKey, and see the [Rest interface documentation for details](https://github.com/BingX-API/BingX-spot-api-doc/blob/master/REST%20API.md#generate-listen-key).


The base URL of Websocket Market Data is: `wss://open-api-ws.bingx.com/market`

User Data Streams are accessed at `/market?listenKey=`

```
wss://open-api-ws.bingx.com/market?listenKey=a8ea75681542e66f1a50a1616dd06ed77dab61baa0c296bca03a9b13ee5f2dd7
```

## 1. Order Update Streams

**Subscription Type**
```
dataType is spot.executionReport
```


Use following API to fetch and update listenKey:

**Subscription Example**
```
{"id":"e745cd6d-d0f6-4a70-8d5a-043e4c741b40","dataType":"spot.executionReport"}
```

**Push Data**

| Return Parameters         | Field Description                      |  
|--------------  |---------------------------   |
| dataType       | The type of data subscribed, such as spot.executionReport |
| data           | Push Data             |
| e              | Event type             |
| E              | Event time             |
| s              | Symbol               |
| S              | BUY/SELL             |
| o              | MARKET/LIMIT             |
| q              | Original quote order quantity          |
| p              | Original quote order price          |
| x              | NEW/CANCELED/TRADE             |
| X              | NEW/PENDING/PARTIALLY_FILLED/FILLED/CANCELED/FAILED             |
| i              | Order ID              |
| l              | Transaction quantity         |
| z              | Accumulated transaction quantity      |
| L              | Transaction price      |
| n              | Fee           |
| N              | Fee asset        |
| T              | Transaction time             |
| t              | Transaction ID              |
| O              | Order creation time         |
| Z              | Cumulative transaction amount    |
| Y              | Transaction amount      |
| Q              | Original quote order amount     |

```
{
  "data": {
      "e": "executionReport",
      "E": 1499405658658,
      "s": "BTC-USDT",
      "S": "BUY",
      "o": "LIMIT",
      "q": "1.00000000",
      "p": "0.10264410",
      "x": "NEW",
      "X": "NEW",
      "i": 4293153,
      "l": "0.00000000",
      "z": "0.00000000",
      "L": "0.00000000",
      "n": "0",         
      "N": "USDT",        
      "T": 1499405658657,
      "t": -1,           
      "O": 1499405658657,
      "Z": "0.00000000", 
      "Y": "0.00000000", 
      "Q": "0.00000000"  
    },
    "dataType": "spot.executionReport"
}
``` 


## 2. Subscribe to account balance push

**Subscription Type**
```
dataType is ACCOUNT_UPDATE
```

**Subscription Example**
```
{"id":"gdfg2311-d0f6-4a70-8d5a-043e4c741b40","dataType":"ACCOUNT_UPDATE"}
```

```
The field "m" represents the reason for the launch of the event, including the following possible types:

DEPOSIT
WITHDRAW
ORDER
FUNDING_FEE
WITHDRAW_REJECT
ADJUSTMENT
INSURANCE_CLEAR
ADMIN_DEPOSIT
ADMIN_WITHDRAW
MARGIN_TRANSFER
MARGIN_TYPE_CHANGE
ASSET_TRANSFER
OPTIONS_PREMIUM_FEE
OPTIONS_SETTLE_PROFIT
AUTO_EXCHANGE
```

**Push Data**

| return field | field description                      |  
|----|---------------------------   |
| e  | Event Type             |
| E  | Event Time             |
| T  | Transaction               |
| m  | Event reason type              |
| a  | Asset             |
| wb   | Wallet Balance          |
| cw   | Cross Wallet Balance          |
| bc  | Balance Change except PnL and Commission             |

```
{
	"e": "ACCOUNT_UPDATE", // Event Type
	"E": 1671159080000,  // Event Time
	"T": 1671159080818,// Transaction
	"a": {
		"B": [{
			"a": "USDT", // Asset
			"bc": "-123.0", // Balance Change except PnL and Commission
			"cw": "38877420.08041096", // Cross Wallet Balance
			"wb": "38877420.08041096" // Wallet Balance
		}],
		"m": "ASSET_TRANSFER" // Event reason type 
	}
}
``` 

**Remarks**

    For more return error codes, please see the error code description on the homepage.
