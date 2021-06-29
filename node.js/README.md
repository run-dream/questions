- 核心组成

  - [V8](./v8.md) 执行引擎
  - [libuv](./libuv) 事件处理机制
  - [JavaScript](./javascript.md) (ES6)
  - [框架](./framework.md) (KOA)

- 模块机制

  - commonjs

    关键字：

    - require
    - exports

    实现:

    - 路径分析

      - 缓存

    - 文件定位

      - native 模块
      - built-in模块
      - 第三方模块
        - JavaScript 模块
        - C/C++ 扩展模块

    - 编译执行

      - 头尾包装 

        ```js
        (function (exports, require, module, __filename, __dirname){
            ...
        })()
        ```

      - runInContext

  - es6

- 异步IO

  - 单线程

    指的是JavaScript在运行时实际是单线程，而IO是有libuv的IO线程池处理的

  - 实现 libuv

    异步IO

    - iocp @windows
    - epoll + 线程池 @linux

  - 异步模型

    基本要素

    - 事件循环
      - tick
    - 观察者
      - 判断是否有事件要处理的过程就是向这些观察者询问是否有要处理的事件
      - 分类：
        - idle 观察者 process.nextTick
        - IO 观察者 
        - check 观察者 setInmediate setTimeout
    - 请求对象
      - 从JavaScript发起调用到内核完成I/O操作的中间产物
    - IO线程池

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

  - 内存泄漏

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

- web

  - cookie 

    原因: HTTP 无状态协议

    性能:

    - 减少Cookie的大小
    - 为静态组件使用不同的域名
    - 减少DNS查询

  - session

    作用：

    1. 减少数据传输
    2. 隐藏敏感数据

    实现:

    1. 基于Cookie来实现用户和数据的映射
    2. 通过查询字符串来实现浏览器端和服务器端数据的对应

    共享session: Redis/memcached

    安全:

    - 将口令通过私钥加密进行签名
    - XSS漏洞

  - 缓存

    - 添加Expires或Cache-Control到报文头中 强缓存
      - max-age
      - Expires
    -  配置ETags（HTTP1.1）协商缓存
      - If-Modified-Since 304
        - 文件的时间戳改动但内容并不一定改动
        - 时间戳只能精确到秒级别，更新频繁的内容将无法生效
      - If-None-Match/ETag
        - 散列
    - 让Ajax可缓存

  - post

    - content-type
      - application/x-www-form-urlencoded
      - application/json
      - application/xml
      - multipart/form-data boundary
    - 内存限制
      - 流
    - CSRF
      - 添加随机值
    - REST

  - 中间件

    - 洋葱圈模型

  - 页面渲染

    - content-type
    - 模版渲染

- [多进程](https://www.cnblogs.com/tugenhua0707/p/11141076.html)

  - 并发处理模型

    - 同步
    - 复制进程 预复制
    - 多线程
    - 事件驱动

  - child_process

    - 创建

      - spawn
      - folk
      - exec

    - IPC

      - 管道 libuv

        - named pipe @windows
        - *ninx Unix Domain Socket

      - 句柄传递 指向对象的文件描述符 

        作用:

        - 直接复用socket

        - 多个子进程可以同时监听相同端口

        实现:

        - childprocess.send(message, [handler])
        - process.on(message, callback)

        原理:

        - 机制
          - 消息传递
          - IPC
          - node内部事件抽象和还原

        - 端口共同监听
          - SO_REUSEADDR 文件描述符是相同
          - 进程服务是抢占式

  - 集群稳定性

    - 性能问题
    - 多个工作进程的存活状态管理
      - 信号
      - 限量重启
      - 负载均衡 
        - 默认抢占式
        - 扩展 ROUND_ROBIN
    - 工作进程的平滑重启
    - 配置或者静态数据的动态重新载入
      - 第三方数据存储
      - 主动通知

  - cluster

    - child_process + net

- 测试

  - 单元测试
    - assert
    - 测试框架
      - mocha
        - 异步测试 done()
        - 超时设置  setTimeout()
      - jscover
    - 模拟异常 mock
      - before after 替换掉难以模拟的异常
      - mok
  - 性能测试
    - 基准测试 benchmark
    - 压力测试 
      - ab
      - siege
    - 测试数据和业务数据的转换

参考书籍：

《深入浅出Node.js》 朴灵