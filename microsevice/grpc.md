### 基本构成

- 通信协议 

  HTTP/2

- 数据传输方式

  异步多路复用

- 数据压缩格式

  - PB(Protocol Buffer)
  - Json



### [HTTP/2 有什么优势](https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/490)

- **多路复用 (Multiplexing)**

  **多路复用允许同时通过单一的 HTTP/2 连接发起多重的请求-响应消息。**

- **二进制分帧**

  HTTP/2 会将所有传输的信息分割为更小的消息和帧（frame）,并对它们采用二进制格式的编码 ，其中 HTTP1.x 的首部信息会被封装到 HEADER frame，而相应的 Request Body 则封装到 DATA frame 里面。

- **首部压缩**

  HTTP 协议不带有状态，每次请求都必须附上所有信息。所以，请求的很多字段都是重复的，比如Cookie和User Agent，一模一样的内容，每次请求都必须附带，这会浪费很多带宽，也影响速度。

  HTTP/2 对这一点做了优化，引入了头信息压缩机制（header compression）。一方面，头信息使用gzip或compress压缩后再发送；另一方面，客户端和服务器同时维护一张头信息表，所有字段都会存入这个表，生成一个索引号，以后就不发送同样字段了，只发送索引号，这样就提高速度了。

- [**服务端推送**](http://www.ruanyifeng.com/blog/2018/03/http2_server_push.html)

- 安全性



### [ProtoBuffer 格式](https://www.cnblogs.com/shine-lee/p/10701810.html)

- 优点

  - **语言无关、平台无关**

    即 ProtoBuf 支持 Java、C++、Python 等多种语言，支持多个平台

  - **高效**

    即比 XML 更小（3 ~ 10倍）、更快（20 ~ 100倍）、更为简单

  - **扩展性、兼容性好**

    你可以更新数据结构，而不影响和破坏原有的旧程序

- 怎么实现序列化和反序列化

  - proto文件 定义数据结构

  - 每一行field的格式数据格式

    ```protobuf
    required/optional/repeated FieldType FieldName = FieldNumber
    ```

    FieldNumbers 用来唯一标识字段在二进制的格式格式

  - varint编码 + wire type 可变长整形

    - key
      - FieldNumber 和 proto文件对应 **指示了当前是哪个数据成员**
      - wire type 标识字段类型 **知道接下来value部分的长度**
    - value 数据
      - length 只当 wire type = 2时，也就是多个数据的时候有效