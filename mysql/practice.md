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

