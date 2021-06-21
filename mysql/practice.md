### 普通索引和唯一索引，应该怎么选择？

关键词： ***change buffer***

mysql为了节省随机读磁盘的IO消耗将 insert / update / delete 影响的不在 buffer pool 的二级索引页缓存起来

适合写多读少的场景，写完以后不需要马上读

回答:

从性能角度优先考虑非唯一索引，在业务层面进行唯一性校验。原因在于：

1. 对读来说 唯一索引可以找到一个满足条件的记录后就停止查找，但是性能差距不大
2. 对写来说 唯一索引每次都需要校验是否违反唯一性约束，无法使用 change buffer 进行优化



### mysql 为什么有时候会选错索引，怎么解决？

关键词: ***优化器*** ***采样统计*** 

回答：

优化器并不能准确的知道哪个索引区分度比较高，而是通过采用统计来估算，可能是由于统计信息不准确导致的。

解决方案：

1. analyze table 重新统计索引情况
2. 使用force index 强行指定原理
3. 修改语句来引导优化器
4. 增加或者删除索引



### 如何给字符串字段加索引？

关键字： ***前缀索引*** ***索引额外占用的空间*** ***查询效率***

1. 直接创建完整索引 比较占用空间
2. 创建前缀索引 节省空间 但是会增加查询扫描次数 并且不能使用覆盖索引
3. 倒序存储，再创建前缀索引，用于解决字符串本身前缀的区分度不够的问题
4. 创建 hash 字段并添加索引，查询性能稳定，有额外的存储和计算消耗，不支持范围查询



### MySQL为什么会抖动一下

关键字:  ***WAL*** ***flush脏页***

回答：

MySQL在写数据的时候，是先记录redolog，然后先写到内存buffer pool里，等到CPU空闲或者不得不的时候才会flush到磁盘，如redolog满了，buffer pool满了，MySQL正常关闭。目的是为了削减磁盘读写的压力, 将随机写转换成顺序写。

写日志和写内存的时候是正常工作的，刷脏页的时候就可能导致卡顿。

解决方案：

1. 设置脏页比例上限 innodb_max_pages_pct
2. 设置redolog写盘速度  innodb_io_capacity
3. 根据磁盘类型选择是否flush邻页 innodb_flush_neighbors



### 为什么表数据删掉一半，表文件大小不变

关键字: ***表数据*** ***空洞***

回答:

在执行delete的时候，innodb实际上只是将记录标记为可复用，形成空洞，但文件不会变小。随机插入会导致数据页分裂，也会导致产生空洞。

解决方案：

1. 重建表 alter table A engine = InnoDB
2. 消耗很大 建议使用 gh-ost



### count(*) 为什么这么慢，应该怎么办？

关键字：***MVCC*** 

回答:

innodb并没有保存表的记录数，原因是为保证隔离性，不同事务count(*)出来的条数不一样。

性能上 count(*) 有做特殊优化 约等于 count(1) < count(id) < count(字段)



### order by 是怎么工作的？如何避免生成临时表和排序？order by 分页怎么处理？

关键字: ***sort_buffer*** ***temporary*** ***filesort*** ***全字段排序*** ***rowid排序*** ***全覆盖索引***

回答： 

   根据排序的单行长度 max_length_for_sort_data 选择不同的算法

- 全字段排序 
- rowid排序 

  基本流程都是

1. 初始化sort_buffer,确定放入排序字段
2. 通过索引找到主键id
3. 到聚集索引去取数据，将字段放入sort_buffer
4. 从索引获取下一个主键id
5. 重复3，4，直到下一个数据不满足条件
6. 对sort_buffer 中的数据进行排序
7. 返回数据 

如果排序数据量 > sort_buffer_size 则会使用filesort，使用rowid排序可以减小排序数据流

区别在于 放入到sort_buffer里的字段不一样，rowid排序只会放入排序字段，完整数据需要多一次回表。

解决方案：

1. 如何避免生成临时表和排序？

   - 建立覆盖索引 using index
   - 利用索引多查几次 在客户端进行归并排序

2. order by 大数据量分页怎么处理？

   放弃总页数的精确计算，即不使用count

   根据业务选择一个带索引的列来分页，比如id 假设每页查询100条：

   第一页查询：select * from xxx where id>=0 limit 101

   如果返回101条记录，说明有下一页，继续查第二页：

   select * from xxx where id>=? limit 101 其中参数是上一次查询的最后一条记录的id

   查到某一页返回结果数量<101，即为最后一页



### [prepare statement 用sql 怎么写](https://www.cnblogs.com/geaozhang/p/9891338.html)

关键字: ***prepare*** ***execute*** ***deallocate prepare***

```sql
-- 实现随机获取一个元素，会使用临时表 filesort 性能不好
select * from tbl_test order by rand() limit 1;

-- 使用prepare statement 
select count(*) into @C from tbl_test;
set @I = floor(@C * rand());
set @Sql = concat("select * from tbl_test limit", @I, ", 1");
prepare stmt FROM @Sql;
execute @stmt;
deallocate prepare @stmt;
```



### 为什么逻辑相同的语句，性能差异巨大

1. 对索引字段进行函数操作，可能会破坏索引值的有序性，因此优化器放弃走树搜索。
2. 隐式类型转换
3. 隐式字符编码转换



### 为什么只查一行的语句也那么慢

回答：

1. 数据库本身压力就很大
2. 没有走索引
3. 锁
   - 表级锁 DDL等待MDL锁
   - 行级锁 
4. 等 flush
5. 一致性读

解决方案：

```sql
-- 查看语句处于什么状态
show processlist

-- 查询锁等待情况
select * from sys.innodb_lock_waits where locked_table = 'xxx'
-- 断开对应的连接
kill <process-id>
```



### 幻读是什么，有什么问题？

关键字：***当前读*** ***insert***

回答：

 读到其他事务insert已经提交的数据（select for update）

会导致

- 语义错误
- 数据一致性错误 值数据库 和 binlog 对不上

解决方案

- 将隔离级别改为读已提交,并将 binlog_format该车row（不影响业务的情况下）
- 引入间隙锁的概念（可能会导致死锁，影响并发度）



### MySQL的加锁规则是什么

关键字: ***next-key lock*** ***行锁*** ***间隙锁***

回答:

两个原则，两个优化，一个bug

1. 原则：加锁的基本单位是***next-key lock***，即 行锁（锁update） + 间隙锁（锁insert），范围是 (]
2. 原则：查询过程中***访问到的对象***（索引）才会加锁，两个走不同索引的更新不会被锁，走覆盖索引的就不会锁主键索引
3. 优化：等值查询给***唯一索引***加锁的时候，会退化为行锁
4. 优化：等值查询遍历最后一个值不符合条件的时候，会退化为间隙锁
5. bug： 唯一索引上的***范围查询***会访问到不满足条件的第一个值 （8.0.18 版本已经解决此bug）



### 慢查询的原因

1. 索引没设计好
2. SQL没写好
3. MySQL选错了索引



### MySQL是怎么保证数据不丢失的

关键词： ***binlog*** ***redolog*** ***WAL*** ***两阶段提交*** 

回答：

MySQL写数据的时候，首先是写日志和内存，然后在写到磁盘里的。

然后通过redolog prepare, binlog, redolog commit 由xid(server层)关联起来两阶段提交保证。任何一个阶段出错都不影响数据恢复。

恢复时:

1. 按顺序扫描redolog，如果redolog中的事务既有prepare标识，又有commit标识，就直接提交

2. 如果redolog事务只有prepare标识，没有commit标识，则说明当前事务在commit阶段crash了，binlog中当前事务是否完整未可知，此时拿着redolog中当前事务的XID（redolog和binlog中事务落盘的标识），去查看binlog中是否存在此XID

    a. 如果binlog中有当前事务的XID，则提交事务（复制redolog disk中的数据页到磁盘数据页）

    b. 如果binlog中没有当前事务的XID，则回滚事务（使用undolog来删除redolog中的对应事务）

 WAL的好处

- 组提交机制 降低 IOPS消耗
- 顺序读写



### MySQL是怎么保证主备一致的

关键字:  ***binlog*** ***relaylog*** ***dump_thread*** ***io_thread*** ***sql_thread*** ***bg_thread***

回答：

同步流程：

- master部分
  - 主线程
    - undolog mem
    - data mem
    - redolog preprare
    - binlog 
    - redolog commit
  - dump 线程 在 binlog 生成的时候将binlog 发现给 slave

- slave 部分
  - io_thread 将 binlog 写到本币，称为relaylog
  - sql_thread（可多线程） 读取 relaylog，解析出命令，并执行

binlog格式:

工具: mysqlbinlog

|           | 优点   | 缺点                 |
| --------- | ------ | -------------------- |
| STATEMENT | 日志小 | 可能出现unsafe的情况 |
| ROW       | 安全   | 日志大 效率低        |
| MIXED     | 折中   |                      |



### [如何对MySQL进行主备切换](https://www.toutiao.com/i6731338705182654988/?wid=1624087106413)

关键词：***主备延迟 seconds behind master***

步骤: 

1. 查看主库状态，确保binlog都已发送到从库
2. 查看从库状态，确保relaylog都已经执行
3. 从库停止IO_THREAD
4. 原从库切换为主库
5. 确认账号权限
6. 原主库切换为从库
7. 检查主库
8. 启动从库

问题：

切换过程中有新数据插入就会丢失



解决方案：

1. 设置从库readonly
2. binlog格式选择row
3. 引入HA系统



###  MySQL是如何保证高可用的

关键词： ***主备切换逻辑***  ***主备延迟***

回答：

MySQL通过binlog实现了主从复制，可以从库和主库都可以提供查询的能力，当主库宕机的时候，可以将备库切换为主库，从而实现高可用。

但是主备库中可能存在主备延迟，原因可能有：

1. 备库所在机器的性能要比主库所在的机器性能差
2. 备库的压力大
3. 大事务

主备切换的策略：

1. 可靠性优先策略 缺点是受seconds_behind_master的影响可能导致不可用
2. 可用性优先策略 缺点是会导致数据不一致



### 为什么主备延迟到几个小时

关键词： ***备库并行复制能力*** 

回答：

在官方的 5.6 版本之前，MySQL 只支持单线程复制，由此在主库并发高、TPS 高时就会出现严重的主备延迟问题。

MySQL的**复制架构**：

1. 异步复制 master dump线程 slave io，sql线程 协作进行复制 缺点主库的事务执行不会管备库的同步进度，如果备库落后，主库不幸crash，那么就会导致数据丢失
2. 半同步复制 主库在应答客户端提交的事务前需要保证至少一个从库接收并写到relay log中
3. 全同步复制 主库在应答客户端提交的事务前需要保证所有从库接收并写到relay log中

并行复制策略： 

- slave-parallel-type
  1. DATABASE 按库并行策略
  2. LOGICAL_CLOCK 组提交  模拟主库的并行模式
-  binlog-transaction-dependency-tracking
  1. COMMIT_ORDER 根据redolog preprare 和 commit 来判断是否可以并行
  2. WRITESET 表示的是对于事务涉及更新的每一行，计算出这一行的 hash 值，组成集合 writeset。如果两个事务没有操作相同的行，也就是说它们的 writeset 没有交集，就可以并行 （效率最高）
  3. WRITESET_SESSION，是在 WRITESET 的基础上多了一个约束，即在主库上同一个线程先后执行的两个事务，在备库执行的时候，要保证相同的先后顺序



### 主库出问题了，从库怎么办？

关键词： ***GTID*** ***半同步***

回答：

主库挂了，那肯定要从从库中选一个成为主库，也就是主备切换

[MHA工具](https://www.cnblogs.com/--smile/p/11475380.html)

方案：

1. 基于位点的主备切换

   ```sql
   CHANGE MASTER TO 
   MASTER_HOST=$host_name 
   MASTER_PORT=$port 
   MASTER_USER=$user_name 
   MASTER_PASSWORD=$password 
   MASTER_LOG_FILE=$master_log_name 
   MASTER_LOG_POS=$master_log_pos  
   ```

   缺点：需要确定LOG_FILE和LOG_POS,但是往往不能精确获取到

   ```bash
   # 获取FILE
   show master status
   # 获取POS
   mysqlbinlog File --stop-datetime=T --start-datetime=T
   ```

   这个值并不精确,需要跳过重复的事务或者跳过错误

2. 基于GTID的主备切换

   GTID 的全称是 Global Transaction Identifier，也就是全局事务 ID，是一个事务在提交的时候生成的，是这个事务的唯一标识。格式是GTID=server_uuid:gno

   ```sql
   CHANGE MASTER TO 
   MASTER_HOST=$host_name 
   MASTER_PORT=$port 
   MASTER_USER=$user_name 
   MASTER_PASSWORD=$password 
   master_auto_position=1 
   ```

   从库会根据本身的GTID集合来判断事务要不要再执行



### 读写分离有哪些坑

关键词: ***过期读***

回答：

由于主从可能存在延迟，客户端执行完一个更新事务后马上发起查询，如果查询选择的是从库的话，就有可能读到刚刚的事务更新之前的状态。

解决方案：

1. 强制走主库方案
2. sleep 方案
3. 判断主备无延迟方案
4. 配合 semi-sync 方案
5. 等主库位点方案
6. 等 GTID 方案



### 如何判断一个数据库是不是出问题了

回答：

1. select 1 只能判断进程还在
2. 查表判断 可以判断查询正常
3. 更新表判断  timestamp 不够准确
4. 内部统计 performance_schema  对性能有损耗



### 怎么看死锁

回答:

```sql
 show engine innodb status 
```

 LATESTDETECTED DEADLOCK



### 删库了怎么恢复

回答：

1. 删除行 

   flashback。要求 binlog_format=row 和 binlog_row_image=FULL

2. 误删表/库

   用全量备份，加增量日志的方式了。这个方案要求线上有定期的全量备份，并且实时备份 binlog。

3. 延迟复制备库

   延迟复制的备库是一种特殊的备库，通过 CHANGE MASTER TO MASTER_DELAY = N 命令，可以指定这个备库持续保持跟主库有 N 秒的延迟



重要的是不要删库

1. 账号分离
2. 制定操作规范



### KILL的原理

关键词：***信号***

回答：

1. 把 session B 的运行状态改成 THD::KILL_QUERY
2. 给 session B 的执行线程发一个信号



###  MySQL 的查询结果发送给客户端的过程

1. MySQL 采用的是边算边发的逻辑，因此对于数据量很大的查询结果来说，不会在 server 端保存完整的结果集。所以，如果客户端读结果不及时，会堵住 MySQL 的查询过程，但是不会把内存打爆。占用率socket receive buffer
2. 对于 InnoDB 引擎内部，由于有淘汰策略，大查询也不会导致内存暴涨。并且，由于 InnoDB 对 LRU 算法做了改进，冷数据的全表扫描，对 Buffer Pool 的影响也能做到可控。



### JOIN应该用哪个表做索引

回答：

1. 可以使用被驱动表的索引的情况下，采用Index Nested-Loop Join算法，需要让小表做驱动表
2. 被驱动表上没有可用的索引的情况下，采用Block Nested-Loop Join 算法，
   - 在 join_buffer_size 足够大的时候，是一样的
   - 在 join_buffer_size 不够大的时候（这种情况更常见），应该选择小表做驱动表

总是应该使用***小表***做驱动表

更准确地说，在决定哪个表做驱动表的时候，应该是两个表按照各自的条件过滤，过滤完成之后，计算参与 join 的各个字段的总数据量，数据量小的那个表，就是“小表”，应该作为驱动表。



### JOIN 语句怎么优化

关键词: ***MMR*** ***BKA*** 

1. 给被驱动表的关联字段加上索引

   BKA 优化。利用 join_buffer是暂存驱动表的数据,利用MRR的机制去主键索引查数据，才能体现出“顺序性”的优势

2. 基于临时表的改进方案

   对于能够提前过滤出小数据的 join 语句来说

3. 应用端 hash join



### 临时表

1. 建表语法是 create temporary table …
2. 一个临时表只能被创建它的 session 访问，对其他线程不可见
3. 临时表可以与普通表同名
4. session A 内有同名的临时表和普通表的时候，show create 语句，以及增删改查语句访问的是临时表
5. show tables 命令不显示临时表。



### GROUP BY 如何优化

关键词：  ***using temporary*** ***filesort*** ***using index***

回答：

1. 如果对 group by 语句的结果没有排序要求，要在语句后面加 order by null；
2. 尽量让 group by 过程用上表的索引，确认方法是 explain 结果里没有 Using temporary 和 Using filesort
3. 如果 group by 需要统计的数据量不大，尽量只使用内存临时表；也可以通过适当调大 tmp_table_size 参数，来避免用到磁盘临时表
4. 如果数据量实在太大，使用 SQL_BIG_RESULT 这个提示，来告诉优化器直接使用排序算法得到 group by 的结果。

