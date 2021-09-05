### 缓存选型

- Memcache

  Memcache 提供简单的 kv cache 存储，value 大小不超过1mb，可作为大文本或者简单的 kv结构使用。
  Memcache 使用了 [slab](http://c.biancheng.net/view/1284.html) 方式做内存管理，存在一定的浪费，如果大量接近的 item，建议调整 Memcache 参数来优化每一个 slab 增长的 ratio、可以通过设置 slab_automove & slab_reassign 开启 Memcache 的动态/手动 move slab，防止某些 slab 热点导致内存足够的情况下引发 LRU。
  大部分情况下，简单 KV 推荐使用 Memcache，吞吐和相应都足够好。

  每个 slab 包含若干大小为1M的内存页，这些内存又被分割成多个 chunk，每个 chunk 存储一个 item；
  在 Memcache 启动初始化时，每个 slab 都预分配一个 1M 的内存页，由slabs_preallocate 完成（也可将相应代码注释掉关闭预分配功能）。
  chunk 的增长因子由 -f 指定，默认1.25，起始大小为48字节。
  内存池有很多种设计，可以参考下: nginx ngx_pool_t，tcmalloc 的设计等等。

- Redis

  Redis 有丰富的数据类型，支持增量方式的修改部分数据，比如排行榜，集合，数组等。
  比较常用的方式是使用 Redis 作为数据索引，比如评论的列表 ID，播放历史的列表 ID 集合，我们的关系链列表 ID。
  Redis 因为没有使用内存池，所以是存在一定的内存碎片的，一般会使用 jemalloc 来优化内存分配，需要编译时候使用 jemalloc 库代替 glib 的 malloc 使用。

- 对比

  redis 单线程，memcache 多线程，所以 QPS 可能两者差异不大，但是吞吐会有很大的差别，比如大数据 value 返回的时候，redis qps 会抖动下降的的很厉害，因为单线程工作，其他查询进不来。

  所以建议纯 kv 都走 memcache，比如我们的关系链服务中用了 hashs 存储双向关系，但是我们也会使用 memcache 档一层来避免hgetall 导致的吞吐下降问题。

- Proxy
  - twemproxy
    缺点：
    - 二次开发成本难度高，难以于公司运维平台进行深度集成；
    - 不支持自动伸缩，不支持 autorebalance 增删节点需要重启才能生效；
    - 运维不友好，没有控制面板；
  - codis: 只支持 Redis 协议，且需要使用 patch版本的 redis；
    mcrouter: 只支持 memcache 协议，C 开发，与运维集成开发难度高；