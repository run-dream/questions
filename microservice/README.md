### 演变历程

- 单体应用
- SOA
- 微服务 

### 模块

- 服务消费者
- 服务提供者
- 注册中心
- 治理中心



### 基本组件

- [服务描述](./service-describe.md)
  - RESTful API （WIKI/SWAGGER）(Consul)
  - XML 配置 (Dubbo)
  - IDL 文件 ([gRPC](./grpc.md) Protobuf，跨语言)
- [注册中心](./registry-center.md)
  - 发布
  - 订阅
- [服务框架](./grpc.md)
  - 通信协议 
    - TCP
    - UDP
    - HTTP2
  - 数据传输方式
    - 同步/异步
    - 单连接/多路复用
  - 数据压缩格式
    - JSON 序列化
    - Java 对象序列化
    - Protobuf 序列化
- 服务监控
  - 指标收集
  - 数据处理
  - 数据展示
- 服务追踪
  - 基于requestid链
- 服务治理
  - 单机故障 -> 自动摘除故障节点 
  - 单IDC故障 -> 自动切换流量
  - 依赖服务不可用 -> 熔断