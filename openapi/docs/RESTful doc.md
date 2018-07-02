## 开始使用    

REST，即Representational State Transfer的缩写，是目前最流行的一种互联网软件架构。它结构清晰、符合标准、易于理解、扩展方便，正得到越来越多网站的采用。其优点如下：    
- 在RESTful架构中，每一个URL代表一种资源；    
- 客户端和服务器之间，传递这种资源的某种表现层；    
- 客户端通过四个HTTP指令，对服务器端资源进行操作，实现“表现层状态转化”。     
    
## 请求交互    

服务根地址：`http://gw.api.coinmerit.com/`   
	
请求交互说明    
1. 请求参数：根据接口请求参数规定进行参数封装。    
2. 提交请求参数：将封装好的请求参数通过POST 或GET 方式提交至服务器。    
3. 服务器响应：服务器首先对用户请求数据进行参数安全校验，通过校验后根据业务逻辑将响应数据以JSON格式返回给用户。    
4. 数据处理：对服务器响应数据进行处理。 


## 签名算法

1. 请求签名机制

   用户可以从[CoinMerit](https://www.coinmerit.com/)个人中心获取访问密匙（Api Key）与 私密密匙（Secret Key），其中，访问密匙（Api Key） 用于标识访问者的身份；私密密匙（Secret Key）是用于用户端签名以及服务端验证签名的密钥，请注意保管好您的密钥，不要被他人知道。

1. 签名计算方式

    ```
    sign = strtoupper(md5(secret_key . path . timestamp . params . secret_key))
    ```
    1. `secret_key`：服务端验证签名的密钥
    2. `path`： 如：`http://gw.api.coinmerit.com/exchanges` 中即为 `exchanges`
    3. `timestamp`：表示此次操作的时间，不能为空，如果请求时间和服务器时间相差超过15分钟，会返回错误
    5. `params`: 请求参数，首先按照参数键名升序排列，然后参数键值对用`=`链接，多个参数用`&`链接，例如： `$2y$10$Esnx4dnd512.l9mlGO8yKONApEUfihN/lY5adLGUwgJxVOPUaavBOkline1530001964exchange=zb&size=500&symbol=xem_qc$2y$10$Esnx4dnd512.l9mlGO8yKONApEUfihN/lY5adLGUwgJxVOPUaavBOsign:0F8F233DDDC8A43A6783F186C650265A%`
    4. 用来签名的字符串为UTF-8格式
    5. 签名的方法用MD5，并全部转换为大写，例如：E405863D6079147C65E2D4384924B7A8

## 公共参数

以下参数需要在每次请求的Header头中传递，否则系统不会返回数据

|参数名|参数类型|	必填|描述|
|:-----|:-----|:-----|:-----|
|ApiKey|string|是| Api Key 通过登录CoinMerit个人中心获取|
|Timestamp|string|是|请求时的时间戳，精确到秒|
|ApiSign|string|是|请求签名，详见签名算法|

## API列表

1. 获取交易所列表

接口地址： /exchanges

请求方式：GET

请求参数：

```
无
```

示例	

```
# Request
GET http://gw.api.coinmerit.com/exchanges

# Response
{
    "status_code": 200,
    "message": "成功",
    "data": [
        "huobi",
        "binance",
        "zb",
        "okex",
        "bitfinex"
    ]
}
```

返回值说明	

```
无
```



2. 获取交易对列表

接口地址： /currency_pairs

请求方式：GET

请求参数：

|参数名|参数类型|	必填|描述|
|:-----|:-----|:-----|:-----|
|exchange|string|是|交易所编码|

示例	

```
# Request
GET http://gw.api.coinmerit.com/currency_pairs?exchange=zb

# Response
{
    "status_code": 200,
    "message": "成功",
    "data": {
        "exchange": "zb",
        "currency_pairs": [
            "1ST_BTC",
            "1ST_QC",
            "1ST_USDT",
            "ADA_BTC",
            "ADA_QC"
        ]
    }
}
```

返回值说明	

```
exchange:交易所编码
currency_pairs:交易对列表
```

3. 获取K线数据列表

接口地址： /kline

请求方式：GET

请求参数：

|参数名|参数类型|	必填|描述|
|:-----|:-----|:-----|:-----|
|exchange|string|是|交易所编码|
|symbol|string|是|交易对 例如：xem_qc|
|type|string|否|K线数据类型 支持：1min|
|size|int|否|返回条数 支持：1~2000|
|since|int|否|开始时间|

示例	

```
# Request
GET http://gw.api.coinmerit.com/kline?exchange=zb&symbol=xem_qc&size=500

# Response
{
    "status_code": 200,
    "message": "成功",
    "data": [
        [
            1529997840000,
            1.04,
            1.04,
            1.04,
            1.04,
            2.2
        ],
        [
            1529997180000,
            1.06,
            1.06,
            1.06,
            1.06,
            37.5
        ]
    ]
}
```

返回值说明	

```
[
    1529997180000, 时间戳
    1.06,          开 
    1.06,          高
    1.06,          低
    1.06,          收
    37.5           交易量
]
```

## code编码说明

```
400   Api Key 错误
401   未认证或认证不通过
403   ip不在白名单内
404   签名错误

40001 交易所不存在
40002 交易对不存在
40003 不支持的时间
40004 请求时间错误
```