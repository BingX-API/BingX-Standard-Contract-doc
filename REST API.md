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
