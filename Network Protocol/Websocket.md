#Websocket
> WebSocket协议是基于TCP的一种新的网络协议。它实现了浏览器与服务器全双工(full-duplex)通信——允许服务器主动发送信息给客户端。 引用自百度百科

##与http的关系
可以说是http的补充升级包。握手时使用http进行握手请求，握手后的数据传输就走tcp通道传输

![pic](images/websocket.png)
- 相同点：
都是基于http协议经行的握手
- 不同点：
http的生命周期是客户端发起一起请求后服务端响应结果后结束，而websocket则是客户端发起握手协议后与服务端保持长连接，没有数据则挂起监听，
有数据则可以双向发送，任何一端都可以发送结束标识结束生命周期


## 流程
1. 发起握手请求

```
GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==
Sec-WebSocket-Protocol: chat
Sec-WebSocket-Version: 13
Origin: http://example.com
```
与http不同的是多了几个属性：

Upgrade告诉另一端，我这个是升级成websocket的协议，别给搞错了。

Sec-WebSocket-Key则是浏览器生成的一串base64的随机值，告诉服务端别搞错了，是我跟你发起的协议，用来识别对应客户端的。

Sec-WebSocket-Protocol是一个自定义的字符串，用来区分同URL下不同的服务所需要的协议

Sec-WebSocket-Version则是协议的版本了

2. 服务端接受握手请求并返回结果

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: HSmrc0sMlYUkAGmm5OPpG2HaGWk=
Sec-WebSocket-Protocol: chat
```


Sec-WebSocket-Accept表示我已经接受你的握手啦，这是我加密后的密钥。

Sec-WebSocket-Protocol则表示服务器最终接受的子协议


3. 数据传输

握手结束后，就可以双向进行数据传输。

> Websocket协议通过序列化的数据帧传输数据。数据封包协议中定义了opcode、payload length、Payload data等字段。其中要求：
> 1. 客户端向服务器传输的数据帧**必须**进行掩码处理。服务器若接收到未经过掩码处理的数据帧，则必须主动关闭连接。
> 2. 服务器向客户端传输的数据帧**一定不能**进行掩码处理。客户端若接收到经过掩码处理的数据帧，则必须主动关闭连接。

4. 关闭连接

客户端或者服务端都可以发起关闭请求

## 作用
主要是用于实时数据传输的应用开发上(网络游戏数据传输、聊天室)。替代之前的ajax轮询以及长连接(long poll)


## 实现