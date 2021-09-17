三种基本方式：

- RESTful API，用作 HTTP 或者 HTTPS 协议的接口定义。

- XML 配置， 

  1. 服务提供者定义接口，并实现接口。

  2. 服务提供者进程启动时，通过加载 server.xml 配置文件将接口暴露出去。

  3. 服务消费者进程启动时，通过加载 client.xml 配置文件来引入要调用的接口。

- IDL 文件

  接口描述语言（interface description language）的缩写，通过一种中立的方式来描述接口，使得在不同的平台上运行的对象和不同语言编写的程序可以相互通信交流。

  主要是用作跨语言平台的服务之间的调用，有两种最常用的 IDL：

  - Facebook 开源的 Thrift 协议
  -  Google 开源的 [gRPC](./grpc.md) 协议。

