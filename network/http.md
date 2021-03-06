### HTTP 1.1

组成:

- 请求行 

  ``` GET URL CRLF```

- 首部

  ```
  FIELD: Value
  ```

- 正文实体  payload



缺点：

1. 明文传递
2. 连接无法复用 队首阻塞
3. 每次通信都要带完整的 HTTP 的头

### HTTP 2.0

新增特性：

1. 二进制分帧，请求和响应都可以多路复用，有助于解决类似类似队头阻塞的问题
2. 服务器推送，服务器可以对一个客户端请求发送多个响应
3. 首部压缩，使用“首部表”来跟踪和存储之前发送的键－值对

缺点：

1. 基于 TCP 协议的，TCP 协议在处理包时是有严格顺序的，所以帧仍存在阻塞的问题

### QUIC

基于UDP，在应用层实现可靠的连接。

机制：

1. 自定义连接机制，不再需要三次握手，以一个 64 位的随机数作为 ID,替代TCP的源 IP、源端口、目的 IP、目的端口。当 IP 或者端口变化的时候，只要 ID 不变，就不需要重新建立连接。
2. 自定义重传机制，通过递增**序列号**+**偏移量**的机制，替代TCP的序号和应答机制，来解决顺序问题和丢包问题。
3. 无阻塞的多路复用，基于**UDP** 的，一个连接上的多个 stream 之间没有依赖。
4. 自定义流量控制。通过window_update，来告诉对端它可以接受的字节数。但是 QUIC 的窗口是适应自己的多路复用机制的，不但在一个连接上控制窗口，还在一个连接中的每个 stream 控制窗口。

### HTTPS

HTTP协议明文传播，容易被劫持。

- 加密分对称加密和非对称加密。
  - 对称加密效率高，但是解决不了密钥传输问题；
  - 非对称加密可以解决这个问题，但是效率不高。
- 非对称加密需要通过证书和权威机构来验证公钥的合法性。
- HTTPS 是综合了对称加密和非对称加密算法的 HTTP 协议。既保证传输安全，也保证传输效率。



