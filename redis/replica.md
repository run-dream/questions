### Redis 是怎么进行主从复制的

关键词: ***PSYNC*** ***RDB*** ***全量复制*** ***部分重同步*** ***replication buffer 和 repl_backlog_buffer***

回答：

1. 从库执行slaveof/replicaof
2. 从库向主库发送 psync ? -1 表示需要进行全量同步
3. 主库返回FULLRESYNC runId 和 offset，从库记录下这两个值
4. 主库生成rdb文件，并发送给从库
5. 主库将新收到的写命令，记录到replication buffer里，并同步到从库
6. 如果主从库网络断开，库会把断连期间收到的写操作命令，写入 replication buffer，同时也会把这些操作命令也写入 repl_backlog_buffer 这个缓冲区，等到重连的时候就可以增量同步。

replication buffer是用来在主从之间发送数据的，每个从库对应主库的一个replication buffer。

而repl_backlog_buffer是用来持续保存写操作的一块内存，多个从库共享一个repl_backlog_buffer

### Redis 为什么不使用AOF进行主从复制呢

1. AOF 文件大，执行慢
2. AOF 的策略多，如果策略不当可能导致IO压力大