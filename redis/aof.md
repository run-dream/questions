### 什么是AOF？为什么需要AOF? 

定义:

AOF也就是Append On File 日志，和 WAL 不同， AOF 是写后日志，也就是Redis 是先执行命令，把数据写入内存，然后才记录日志。

用途:

AOF是用来解决Redis基于内存的，一旦服务器宕机，内存中的数据将全部丢失。

### AOF 有什么优点?有什么缺点?

优点：

1. 先写成功，再记录日志，可以避免出现记录错误命令的情况。
2. 不会阻塞当前的写操作。



缺点：

1. 如果刚执行完一个命令，还没有来得及记日志就宕机了，那么这个命令和相应的数据就有丢失的风险
2. AOF 虽然避免了对当前命令的阻塞，但可能会给下一个操作带来阻塞风险。这是因为，AOF 日志也是在主线程中执行的，如果在把日志文件写入磁盘时，磁盘写压力大，就会导致写盘很慢，进而导致后续的操作也无法执行了



因此 AOF 提供了三种写回策略:

|          | 策略                                                         | 优点         | 缺点                                                  |
| -------- | ------------------------------------------------------------ | ------------ | ----------------------------------------------------- |
| Always   | 同步写回：每个写命令执行完，立马同步地将日志写回磁盘         | 基本不丢数据 | 影响主线程性能                                        |
| Everysec | 每秒写回：每个写命令执行完，只是先把日志写到 AOF 文件的内存缓冲区，每隔一秒把缓冲区中的内容写入磁盘 | 折中         | 折中                                                  |
| No       | 操作系统控制的写回：每个写命令执行完，只是先把日志写到 AOF 文件的内存缓冲区，由操作系统决定何时将缓冲区内容写回磁盘。 | 性能压力小   | 只要 AOF 记录没有写回磁盘，一旦宕机对应的数据就丢失了 |

### 什么是AOF rewrite？ 怎么进行的?

解决问题：

AOF日志过大

方案：

***重写***

将多个命令，重写成等效的一条命令。

***一个拷贝，两处日志***

每次 AOF 重写时，Redis 会先执行一个内存COW拷贝，用于重写；

然后，使用两个日志保证在重写过程中，新写入的数据不会丢失。而且，因为 Redis 采用额外的子进程程进行数据重写，所以，这个过程并不会阻塞主进程。

### [什么是 COW?](https://juejin.cn/post/6844903702373859335)

fork出的子进程共享父进程的物理空间，当父子进程**有内存写入操作时**，read-only内存页发生中断，**将触发的异常的内存页复制一份**(其余的页还是共享父进程的)。

fork出的子进程功能实现和父进程是一样的。如果有需要，我们会用`exec()`把当前进程映像替换成新的进程文件，完成自己想要实现的功能。