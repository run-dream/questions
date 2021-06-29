### 什么是libuv

#### 定义

libuv 是一个跨平台支持库，原先为 NodeJS 而写。它围绕着事件驱动的异步I/O模型而设计。

### 结构

- 事件

  - Network I/O

  - File I/O
  - DNS Ops
  - User code

- 异步IO

  - IOCP @windows
  - IO 多路复用机制 + IO 线程池 @ *nix
    - epoll @linux
    - kqueue @ freebsd
    - event ports@solaris 10



### 为什么使用 epoll

同步非阻塞IO

- select 遍历数组

- poll 遍历链表

- epoll 红黑树  

  - epoll_create

  - epoll_ctrl

  - epoll_wait 

    一旦基于某个文件描述符就绪时，内核会采用类似callback的回调机制，迅速激活这个文件描述符，当进程调用epoll_wait()时便得到通知。