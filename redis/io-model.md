### Linux 有哪些IO 模型 ？ 区别是什么?

I/O 操作通常包含以下两个步骤：

1. 网卡 <-> 内核缓冲区
2. 内核缓冲区 <-> 用户空间

我们通过数据在用户和内核空间之间复制的时候是不是会阻塞当前用户进程，如果会，则是同步 I/O，否则，就是异步 I/O。

共有五种IO模型:

- 阻塞 I/O (Blocking I/O)

  一个用户进程在发起一个 I/O 操作之后，只有收到响应或者超时才可进行处理其它事情，否则 I/O 将会一直阻塞。

- 非阻塞 I/O (Nonblocking I/O)

  一个用户进程发起一个 I/O 操作之后，如果数据没有就绪，会立刻返回（标志数据资源不可用），此时 CPU 时间片可以用来做一些其它事情。通过重复轮询 I/O来判断。

- I/O 多路复用 (I/O multiplexing)

  这里的 I/O 通常指网络 I/O，多路指多个 Socket 链接，复用指操作系统进行运算调度的最小单位**线程**。整体意思也就是多个网络 I/O 复用一个或少量的线程来处理 Socket。

- 信号驱动 I/O (Signal driven I/O)

  仅在 Unix 上支持，与 I/O 多路复用相比避免了 select 的阻塞轮询。

- 异步 I/O (Asynchronous I/O)

  应用程序发起系统调用后无需等待直接返回当前调用状态，进行后续的其它任务，结果由内核完成 I/O 操作之后通过回调通知到我们的应用程序

### IO 多路复用的方案有哪些？ 区别是什么？

I/O 多路复用的核心设计是 1 个线程处理所有连接的 `等待消息准备好` I/O 事件。

支持**单一线程同时监听多个文件描述符（I/O 事件）**，阻塞等待，并在其中某个文件描述符可读写时收到通知。

方案有：

- select

  采用了数组fd_set来表示文件描述符, 存在最大文件描述符数量限制。且每次调用select都需要把 fd 集合从用户态拷贝到内核态。

- poll

  用链表pollfd的方式解决了最大文件描述符数量限制。

- epoll

  则不需要遍历，基于红黑树, 采用的是回调机制，通过三个系统调用

  - epoll_create 创建一个 epoll 实例并返回 epollfd
  - epoll_ctl 注册 file descriptor 等待的 I/O 事件(比如 EPOLLIN、EPOLLOUT 等) 到 epoll 实例上
  - epoll_wait 则是阻塞监听 epoll 实例上所有的 file descriptor 的 I/O 事件，它接收一个用户空间上的一块内存地址 (events 数组)，kernel 会在有 I/O 事件发生的时候把文件描述符列表复制到这块内存地址上，然后 epoll_wait 解除阻塞并返回，最后用户空间上的程序就可以对相应的 fd 进行读写了。

  分清了高频调用和低频调用，直接返回已就绪 fd，并减少了数据在用户态和内核态之间的复制。

### [Redis 是怎么实现的IO 多路复用？](https://draveness.me/redis-io-multiplexing/)

事件驱动。



### 参考文档

[I/O 模型浅谈](https://www.bookstack.cn/read/Nodejs-Roadmap/nodejs-IO.md)

[Go netpoller 原生网络模型之源码全面揭秘](https://strikefreedom.top/go-netpoll-io-multiplexing-reactor)