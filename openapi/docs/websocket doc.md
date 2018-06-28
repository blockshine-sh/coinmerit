## 开始使用    

WebSocket是HTML5一种新的协议(Protocol)。它实现了客户端与服务器全双工通信，使得数据可以快速地双向传播。通过一次简单的握手就可以建立客户端和服务器连接，服务器根据业务规则可以主动推送信息给客户端。其优点如下：   
- 客户端和服务器进行数据传输时，请求头信息比较小，大概2个字节；   
- 客户端和服务器皆可以主动地发送数据给对方；   
- 不需要多次创建TCP请求和销毁，节约宽带和服务器的资源。      
    
## 请求交互    

WebSocket服务连接地址：`ws://gw.api.coinmerit.com/websocket`         

注意：连接时需要在header头中添加参数ApiKey，可以从CoinMerit个人中心获取。

#### 发送请求    
请求数据格式为：  
```json
{
	"event": "subscribe",
	"topic": "cm_huobi_eth_usdt_kline_1min"
}
``` 
其中

event: subscribe(订阅数据)/unsubscribe(取消订阅数据)   
topic: 订阅数据主题 
    
例如： 
`websocket.send('{"event":"subscribe","topic":"cm_huobi_eth_usdt_kline_1min"}')`  

#### 服务器响应    
返回数据格式为：

```json
{"result":true,"error_code":0}
```

其中  
result: true成功，false失败  
error_code: 错误码  

#### 如何判断连接是否断开
CoinMerit通过心跳机制解决这个问题。客户端每30秒发送一次心跳数据：{'event':'ping'}，服务器会响应客户端：{"event":"pong"} 以此来表明客户端和服务端保持正常连接。如果客户端未接到服务端响应的心跳数据则需要客户端重新建立连接。    

## API参考  

1. cm_X_Y_kline_Z   订阅K线数据

X值为交易所，如huobi	

Y值为交易对，如eth_usdt

Z值为K线时间周期，如1min			

示例	

```
# Request

websocket.send('{"event":"subscribe","topic":"cm_huobi_eth_usdt_kline_1min"}')

# Response
{
	"topic": "cm_huobi_eth_usdt_kline_1min",
	"data": [1529995200000, 0.6371, 0.64, 0.6371, 0.6398, 24693.3]
}
```

返回值说明	

```
topic:订阅数据主题

data:
[时间,开盘价,最高价,最低价,收盘价,成交量]
[string, string, string, string, string, string]
```


