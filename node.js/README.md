- 模块机制
  - commonjs
  - es6

- 异步IO

  - 单线程
  - 事件循环
  - 观察者
  - I/O线程池

- 非I/O的异步API

  - setTimeout
  - setInterval
  - setImmediate
  - process.nextTick

- 异步编程

  - 异步并发控制

- 内存控制

  - V8

  - 垃圾回收机制

    - 新生代
      - Scavenge算法
    - 老生代
      - mark-sweep
      - Mark-compact
      - Incremental Marking

  - 作用域

    - 全局作用域
    - 闭包

  - 内存指标

    方法：

    - process.memoryUsage
    - os.totalmem os.freemem

    分类:

    - V8进行分配的部分
    - Node自行分配的部分 比如 Buffer

  -  内存泄漏

    - 原因

      1. 缓存
         - 缓存限制策略
         - 外部缓存的好处
           - 减少常驻内存的对象的数量，让垃圾回收更高效
           - 进程之间可以共享缓存。-

      2. 队列消费不及时

      3. 作用域未释放

- 如何排查
  - 工具
    - V8-profiler
    - node-heapdump
    - node-memwatch
  - 思路
    - 内存快照
    - gc
- 大内存应用
  - stream
  - buffer

- Buffer
  - 结构
    - js分配 c++申请
    - 堆外内存
  - 字符串转换
    - toString()
    - iconv/iconv-lite
    - buffer中文乱码处理
      - setEncoding() string_decoder 会按照编码处理
      - 将多个小Buffer对象拼接为一个Buffer对象，然后通过iconv-lite一类的模块来转码这种方式
  - 性能
    - 通过预先转换静态内容为Buffer对象，可以有效地减少CPU的重复使用，节省服务器资源。
    - 读取一个相同的大文件时，highWaterMark值的大小与读取速度的关系：该值越大，读取速度越快。
- 网络
  - 模块
    - net -> tcp
    - dgram -> udp
    - http 
      - request
    - https
  - 网络安全
    - tls/ssl(openssl)
      - 密钥 非堆成加密
      - 数据证书 ca颁发

参考书籍：

《深入浅出Node.js》 朴灵