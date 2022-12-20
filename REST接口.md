<!-- TOC -->

- [基础信息](#基础信息)
    - [服务地址](#服务地址)
    - [常见错误码](#常见错误码)
- [签名认证](#签名认证)
    - [签名说明](#签名说明)
- [标准合约接口](#标准合约接口)
    - [持仓](#持仓)
    - [历史订单](#历史订单)
    - [查询标准合约余额](#查询标准合约余额)
    

<!-- TOC -->

# 基础信息
## 服务地址

https://open-api.bingx.com

HTTP状态码200表示成功响应，并可能包含内容。如果响应含有内容，则将显示在相应的返回内容里面。

## 服务申请

目前API内测中，申请页面即将开放，请耐心等待。如有其他需求，欢迎联系客服。

## 常见错误码

**常见HTTP错误码**

* 4XX 错误码用于指示错误的请求内容、行为、格式

* 5XX 错误码用于指示服务侧的问题

**常见业务错误码**

* 100001 - 签名验证失败

* 100202 - 余额不足

* 100400 - 参数错误

* 100440 - 下单价格跟市场市场价格偏离太远

* 100500 - 服务器内部错误

* 100503 - 服务器繁忙

**注意**:
* 如果失败，response body 带有错误描述信息

* 每个接口都有可能抛出异常

# 签名认证
请求需要鉴权的接口必须包含以下信息：

* 请求头带上 `X-BX-APIKEY` 传递 `apiKey`。
* 请求参数带上 `signature` 使用签名算法得出的签名。
* 请求参数带上 `timestamp` 作为请求的时间戳，单位是毫秒。服务器收到请求时会判断请求中的时间戳，如果是5000毫秒之前发出的，则请求会被认为无效。这个时间空窗值可以通过发送可选参数`recvWindow`来定义。

## 签名说明
`signature` 请求参数使用 **HMAC SHA256** 方法加密而得到的。

**例如：对于下单请求参数进行签名**
- 接口参数:
```
quoteOrderQty = 20
side = BUY
symbol = ETH-USDT
timestamp = 1649404670162
type = MARKET
```
- api信息:
```
apiKey = Zsm4DcrHBTewmVaElrdwA67PmivPv6VDK6JAkiECZ9QfcUnmn67qjCOgvRuZVOzU
secretKey = UuGuyEGt6ZEkpUObCYCmIfh0elYsZVh80jlYwpJuRZEw70t6vomMH7Sjmf94ztSI
```
- 参数通过`query string`发送示例
```
1. 对接口参数进行拼接: quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET
2. 对拼接好的参数字符串使用secretKey生成签名: 428a3c383bde514baff0d10d3c20e5adfaacaf799e324546dafe5ccc480dd827
   echo -n "quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET" | openssl dgst -sha256 -hmac "UuGuyEGt6ZEkpUObCYCmIfh0elYsZVh80jlYwpJuRZEw70t6vomMH7Sjmf94ztSI" -hex
3. 发送请求: curl -H 'X-BX-APIKEY: Zsm4DcrHBTewmVaElrdwA67PmivPv6VDK6JAkiECZ9QfcUnmn67qjCOgvRuZVOzU' 'https://open-api.bingx.com/openApi/spot/v1/trade/order?quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET&signature=428a3c383bde514baff0d10d3c20e5adfaacaf799e324546dafe5ccc480dd827'
```
- 参数通过`request body`发送示例
```
1. 对接口参数进行拼接: quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET
2. 对拼接好的参数字符串使用secretKey生成签名: 428a3c383bde514baff0d10d3c20e5adfaacaf799e324546dafe5ccc480dd827
   echo -n "quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET" | openssl dgst -sha256 -hmac "UuGuyEGt6ZEkpUObCYCmIfh0elYsZVh80jlYwpJuRZEw70t6vomMH7Sjmf94ztSI" -hex
3. 发送请求: curl -H 'X-BX-APIKEY: Zsm4DcrHBTewmVaElrdwA67PmivPv6VDK6JAkiECZ9QfcUnmn67qjCOgvRuZVOzU' -X POST 'https://open-api.bingx.com/openApi/spot/v1/trade/order' -d 'quoteOrderQty=20&side=BUY&symbol=ETHUSDT&timestamp=1649404670162&type=MARKET&signature=428a3c383bde514baff0d10d3c20e5adfaacaf799e324546dafe5ccc480dd827'
```
- 参数通过`query string`和`request body`发送示例
```
queryString: quoteOrderQty=20&side=BUY&symbol=ETHUSDT
requestBody: timestamp=1649404670162&type=MARKET



1. 对接口参数进行拼接: quoteOrderQty=20&side=BUY&symbol=ETHUSDTtimestamp=1649404670162&type=MARKET
2. 对拼接好的参数字符串使用secretKey生成签名: 94e0b4925060a615e1e372d4c929015d4b59d3c89067dc0beeafcfb33a6d8d10
   echo -n "quoteOrderQty=20&side=BUY&symbol=ETHUSDTtimestamp=1649404670162&type=MARKET" | openssl dgst -sha256 -hmac "UuGuyEGt6ZEkpUObCYCmIfh0elYsZVh80jlYwpJuRZEw70t6vomMH7Sjmf94ztSI" -hex
3. 发送请求: curl -H 'X-BX-APIKEY: Zsm4DcrHBTewmVaElrdwA67PmivPv6VDK6JAkiECZ9QfcUnmn67qjCOgvRuZVOzU' -X POST 'https://open-api.bingx.com/openApi/spot/v1/trade/order?quoteOrderQty=20&side=BUY&symbol=ETHUSDT' -d 'timestamp=1649404670162&type=MARKET&signature=94e0b4925060a615e1e372d4c929015d4b59d3c89067dc0beeafcfb33a6d8d10'
```


# 标准合约接口

## 持仓

**接口**
```
    GET /openApi/contract/v1/allPosition
```

**参数**

无

**响应**

| 参数名                 | 类型       | 备注     |
| ------                | ------    |  ------ |    
| symbol                | string    | 交易品种 |
| initialMargin         | number    | 保证金 |
| leverage              | number    | 杠杆数 |
| unrealizedProfit      | number    | 持仓未实现盈亏 |
| isolated              | bool      | 是否是逐仓模式 |
| entryPrice            | number    | 持仓成本价 |
| positionSide          | number    | 持仓方向，LONG 和 SHORT |
| positionAmt           | number    | 已成交数据|
| currentPrice          | number    | 当前价.没有平仓价时会返回当前价 |
| time                  | int64     | 开仓时间 |
例子：

```
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
```

## 历史订单

**接口**
```
    GET /openApi/contract/v1/allOrders
```

**参数**

| 参数名                 | 类型       | 备注     |
| ------                | ------    |  ------ |   
| symbol                | string    | 币对，格式类似：BTC-USDT，必传 | 
| orderId                | int64    | 订单ID，选填 | 
| startTime                | int64    | 开始时间，选填 | 
| endTime                | int64    | 结束时间，选填 | 
| limit                | int64    | 数量，选填 | 

**响应**

| 参数名                 | 类型       | 备注     |
| ------                | ------    |  ------ |    
| avgPrice      | number    | 平仓价 |
| cumQuote      | number    | 交易额 |
| executedQty   | number    | 成交量 |
| orderId       | number    | 系统订单号 |
| positionSide  | string      | 持仓方向，LONG 和 SHORT |
| status        | string    | 订单状态 CLOSED |
| symbol    | string    | 币对，格式类似：BTC-USDT |
| time  | int64    | 订单时间|
| updateTime    | int64    | 更新时间 |
| margin    | number     | 保证金 |
| leverage  | number     | 杠杆数 |
| isolated  | bool     | 是否是逐仓模式 |
| closePrice    | number     | 平仓价 |
| positionId    | int64     | 持仓订单号 |

例子：

```
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
```

## 查询标准合约余额

**接口**
```
    GET /openApi/contract/v1/balance
```

**参数**

无

**响应**

| 参数名                 | 类型     | 备注         |
| ------                |--------|------------|    
| asset                | string | 资产         |
| balance         | string | 总余额        |
| crossWalletBalance              | string | 全仓余额       |
| crossUnPnl      | string | 全仓持仓未实现盈亏  |
| availableBalance              | string | 下单可用余额     |
| maxWithdrawAmount            | string | 最大可转出余额    |
| marginAvailable          | bool   | 是否可用作联合保证金 |
| updateTime           | number | 时间戳        |

例子：

```
{
    "code": 0,
    "timestamp": 1666421703835,
    "data": [
        {
            "asset": "USDT",        // 资产
            "balance": "122607.35137903",   // 总余额
            "crossWalletBalance": "23.72469206", // 全仓余额
            "crossUnPnl": "0.00000000"  // 全仓持仓未实现盈亏
            "availableBalance": "23.72469206",       // 下单可用余额
            "maxWithdrawAmount": "23.72469206",     // 最大可转出余额
            "marginAvailable": true,    // 是否可用作联合保证金
            "updateTime": 1617939110373
        }
    ]
}
```

