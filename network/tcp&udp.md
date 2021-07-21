### UDP/TCP 的关系

- 相同点

  1. 都是传输层协议
  2. 都根据端口号，将数据交给相应的应用程序

- 不同点

  1. 面向连接

     TCP 面向连接 在客户端和服务端建立数据结构来维护双方交互的状

     UDP 面向无连接

  2. 可靠交互

     TCP 提供可靠交付。

     UDP 继承了 IP 包的特性，不保证不丢失，不保证按顺序到达。

  3. 面向字节流/基于数据报

     TCP 是面向字节流的。发送的时候发的是一个流，没头没尾。

     而 UDP 继承了 IP 的特性，基于数据报的，一个一个地发，一个一个地收。

  4. 拥塞控制

     TCP 是可以有拥塞控制的。包丢弃了或者网络的环境不好了，就会根据情况调整自己的行为

  5. 有无状态

     TCP 其实是一个有状态服务，UDP 则是无状态服务



### UDP 的特点

1. 简单 

   不建立连接，包的标题很短，只有8个字节。

   UDP 具有较好的实时性，网络开销也小

2. 不可靠 

   本身不对数据

   UDP 在传输数据前不建立连接，不对数据报进行检查与修改，无须等待对方的应答，所以会出现分组丢失、重复、乱序，应用程序需要负责传输可靠性方面的所有工作

3. 无拥塞控制

   在发送端，UDP传送数据的速度仅仅是受应用程序生成数据的速度、计算机的能力和传输带宽的限制





### UDP的使用场景

1. 需要资源少，在网络情况比较好的内网，或者对于丢包不敏感的应用 

   比如 DHCP，TFTP

2. 不需要一对一沟通，建立连接，而是可以广播的应用

3. 需要处理速度快，时延低，可以容忍少数丢包，但是要求即便网络拥塞，也毫不退缩，一往无前的时候



### UDP的使用案例

1. 网页或者 APP 的访问

   QUIC 快速 UDP 互联网连接

   原因: 即使使用HTTP2的共享连接，由于TCP 的严格顺序策略使得哪怕共享通道，无关的数据也还是要等到前一个数据处理好。

2. 流媒体的协议

   直播

   原因：即使部分丢帧，视频也能正常播放，对实时性的要求大于部分丢帧

3. 实时游戏

   原因: 游戏对实时要求较为严格，用户不能接受卡死

4. IOT物联网

   物联网通信协议 Thread

   原因: 终端资源少,维护 TCP 协议代价太大

5. 移动通信领域

   GTP-U

   原因: GTP 协议本身就包含复杂的手机上线下线的通信协议。如果基于 TCP，TCP 的机制就显得非常多余



### TCP三次握手

``` mermaid
sequenceDiagram
participant C as Client
participant S as Server
C ->> S: SYN, seq = x 
S ->> C: SYN, seq = y, ack = x + 1
C ->> S: ACK, seq = x+1, ack = y + 1
C -> S: 数据交互
Note right of S: CLOSED <br/> LISTEN <br/> SYN_RCVD<br/> ESTABLISHED <br/>
Note left of C: CLOSED <br/> SYN_SEND <br/> ESTABLISHED <br/>
```

- 第一次握手(SYN, seq=x):

  客户端发送一个 TCP 的 SYN 标志位置1的包，指明客户端打算连接的服务器的端口，以及初始序号 X,保存在包头的序列号(Sequence Number)字段里。

  发送完毕后，客户端进入 `SYN_SEND` 状态。

- 第二次握手(SYN, ACK, seq=y, ack=x+1):

  服务器发回确认包(ACK)应答。即 SYN 标志位和 ACK 标志位均为1。服务器端选择自己 ISN 序列号，放到 Seq 域里，同时将确认序号(Acknowledgement Number)设置为客户的 ISN 加1，即X+1。 发送完毕后，服务器端进入 `SYN_RCVD` 状态。

- 第三次握手(ACK，ack=y+1)

  客户端再次发送确认包(ACK)，SYN 标志位为0，ACK 标志位为1，并且把服务器发来 ACK 的序号字段+1，放在确定字段中发送给对方，并且在数据段放写ISN的+1

  发送完毕后，客户端进入 `ESTABLISHED` 状态，当服务器端接收到这个包时，也进入 `ESTABLISHED` 状态，TCP 握手结束。

### TCP 四次挥手

``` mermaid
sequenceDiagram
participant C as Client
participant S as Server
C -> S: 数据传输
C ->> S : FIN, seq = p
S ->> C : ACK, ack = p + 1
S ->> C : FIN, seq = q, ack = p + 1
C ->> S : ACK, seq = q + 1
note left of C: ESTABLISHED <br/> FIN_WAIT_1 <br/> FIN_WAIT_2 <br/> TIME_WAIT <br/> CLOSED
note right of S: ESTABLISHED <br/> CLOSED_WAIT <br/> LAST_ACK <br/> CLOSED
```

- 第一次挥手(FIN=1，seq=x)

  假设客户端想要关闭连接，客户端发送一个 FIN 标志位置为1的包，表示自己已经没有数据可以发送了，但是仍然可以接受数据。

  发送完毕后，客户端进入 `FIN_WAIT_1` 状态。

- 第二次挥手(ACK=1，ACKnum=x+1)

  服务器端确认客户端的 FIN 包，发送一个确认包，表明自己接受到了客户端关闭连接的请求，但还没有准备好关闭连接。

  发送完毕后，服务器端进入 `CLOSE_WAIT` 状态，客户端接收到这个确认包之后，进入 `FIN_WAIT_2` 状态，等待服务器端关闭连接。

- 第三次挥手(FIN=1，seq=y)

  服务器端准备好关闭连接时，向客户端发送结束连接请求，FIN 置为1。

  发送完毕后，服务器端进入 `LAST_ACK` 状态，等待来自客户端的最后一个ACK。

- 第四次挥手(ACK=1，ACKnum=y+1)

  客户端接收到来自服务器端的关闭请求，发送一个确认包，并进入 `TIME_WAIT`状态，等待可能出现的要求重传的 ACK 包。

  服务器端接收到这个确认包之后，关闭连接，进入 `CLOSED` 状态。

  客户端等待了某个固定时间（两个最大段生命周期，2MSL，2 Maximum Segment Lifetime）之后，没有收到服务器端的 ACK ，认为服务器端已经正常关闭连接，于是自己也关闭连接，进入 `CLOSED` 状态。



### [为什么是***三次***握手](https://mp.weixin.qq.com/s/NIjxgx4NPn7FC4PfkHBAAQ)

一句话概括，TCP连接握手，握的是通信***双方数据原点的序列号***

第一次握手 服务端知道了客户端的初始序列号，并且验证了客户端发消息的能力

第二次握手 客户端知道了服务端的初始序列号，并且验证了服务端收发消息的能力

第三次握手 服务端验证了客户端受消息的能力 

### TCP 协议是如何保证可靠性的

TCP提供一种面向连接的、可靠的字节流服务。其中，面向连接意味着两个使用TCP的应用（通常是一个客户和一个服务器）在彼此交换数据之前必须先建立一个TCP连接。在一个TCP连接中，仅有两方进行彼此通信；而字节流服务意味着两个应用程序通过TCP链接交换8bit字节构成的字节流，TCP不在字节流中插入记录标识符。 

- 数据包校验

  目的是检测数据在传输过程中的任何变化，若校验出包有错，则丢弃报文段并且不给出响应，这时TCP发送数据端超时后会重发数据

- 对失序数据包重排序

  既然TCP报文段作为IP数据报来传输，而IP数据报的到达可能会失序，因此TCP报文段的到达也可能会失序。TCP将对失序数据进行重新排序，然后才交给应用层

- 丢弃重复数据

  对于重复数据，能够丢弃重复数据

- 应答机制

  当TCP收到发自TCP连接另一端的数据，它将发送一个确认。这个确认不是立即发送，通常将推迟几分之一秒

- 超时重发

  当TCP发出一个段后，它启动一个定时器，等待目的端确认收到这个报文段。如果不能及时收到一个确认，将重发这个报文段

- 当TCP发出一个段后，它启动一个定时器，等待目的端确认收到这个报文段。如果不能及时收到一个确认，将重发这个报文段



### 累计应答

为了保证顺序性，每一个包都有一个 ID。在建立连接的时候，会商定起始的 ID 是什么，然后按照 ID 一个个发送。为了保证不丢包，对于发送的包都要进行应答，但是这个应答也不是一个一个来的，而是会应答某个之前的 ID，表示都收到了，这种模式称为累计确认或者累计应答



### 确认与重发的机制

1. 超时重试

   也即对每一个发送了，但是没有 ACK 的包，都有设一个定时器，超过了一定的时间，就重新尝试。

   自适应重传算法 采样 RTT

2. 快速重传

   接收方收到一个序号大于下一个所期望的报文段时，就会检测到数据流中的一个间隔，于是它就会发送冗余的 ACK，仍然 ACK 的是期望接收的报文段。而当客户端收到三个冗余的 ACK 后，就会在定时器过期之前，重传丢失的报文段。

3. Selective Acknowledgment （SACK）

   在 TCP 头里加一个 SACK 的东西，可以将缓存的地图发送给发送方



### 流量控制

**滑动窗口**机制。接收窗口rwnd。

滑动窗口，其实就是接收方告诉发送方自己的网络包的接收能力，超过这个能力，我就受不了了。

因为滑动窗口的存在，将发送方的缓存分成了四个部分。

1. 发送了并且已经确认的。这部分是已经发送完毕的网络包，这部分没有用了，可以回收。
2. 发送了但尚未确认的。这部分，发送方要等待，万一发送不成功，还要重新发送，所以不能删除。
3. 没有发送，但是已经等待发送的。这部分是接收方空闲的能力，可以马上发送，接收方收得了。
4. 没有发送，并且暂时还不会发送的。这部分已经超过了接收方的接收能力，再发送接收方就收不了了。

因为滑动窗口的存在，接收方的缓存也要分成了三个部分。

1. 接受并且确认过的任务。这部分完全接收成功了，可以交给应用层了。
2. 还没接收，但是马上就能接收的任务。这部分有的网络包到达了，但是还没确认，不算完全完毕，有的还没有到达，那就是接收方能够接受的最大的网络包数量。
3. 还没接收，也没法接收的任务。这部分已经超出接收方能力。

在网络包的交互过程中，接收方会将第二部分的大小，作为 AdvertisedWindow 发送给发送方，发送方就可以根据他来调整发送速度了。

### 拥塞控制

**慢启动**。

慢启动为发送方的TCP增加了另一个窗口：拥塞窗口(congestion window)，记为cwnd。当与另一个网络的主机建立TCP连接时，拥塞窗口被初始化为1个报文段（即另一端通告的报文段大小）。每收到一个ACK，拥塞窗口就增加一个报文段（cwnd以字节为单位，但是慢启动以报文段大小为单位进行增加）。发送方取拥塞窗口与通告窗口中的最小值作为发送上限。

发送方开始时发送一个报文段，然后等待ACK。当收到该ACK时，拥塞窗口从1增加为2，即可以发送两个报文段。当收到这两个报文段的ACK时，拥塞窗口就增加为4。这是一种指数增加的关系。