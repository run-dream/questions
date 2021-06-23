### Redis 知识全景
- 应用维度
  - 缓存
  - 集群
  - 数据结构
- 系统维度
  - 高性能
    - [epoll](./io-model.md)
    - [数据结构](./data-type.md)
    - [线程模型](thread-model.md)
  - 高可用
    - [AOF](./aof.md) [RDB](./rdb.md)
    - [哨兵机制](./sentinel.md)
    - [主从复制](./replica.md)
  - 高可扩展
    - 负载均衡
    - 数据分片


### Redis 问题全景

- 主从库
  - 数据丢失
  - 主从不一致
- 性能
  - 阻塞
  - 抖动
  - bigkey
- 内存
  - 占用飙升
- 缓存
  - 污染
  - 雪崩
  - 穿透
- 切片集群
  - 热点数据
  - 秒杀