### [Redis 的数据类型 和 数据编码 数据结构的关系是什么](https://segmentfault.com/a/1190000020770894)

命令:

```bash
OBJECT ENCODING <KEY>
```

Redis共有5种数据类型, 8种数据编码 

1. String

   数据编码:

   - REDIS_ENCODING_INT  

     数据结构: long类型的整数 

     条件: isInt(val)

   - REDIS_ENCODING_EMBSTR

     数据结构: sds  redisobject和raw在一块儿内存中

     条件: len <= 39

   - REDIS_ENCODING_RAW sds  len > 39

     数据结构: sds  redisobject和 sds 各分配一块内存

     条件: len > 39

   命令:

   ```bash
   # 设置指定 key 的值
   SET key value
   # 获取指定 key 的值
   GET key
   # 返回 key 中字符串值的子字符
   GETRANGE key start end
   # 将给定 key 的值设为 value ，并返回 key 的旧值(old value)
   GETSET key value
   # 对 key 所储存的字符串值，获取指定偏移量上的位(bit)
   GETBIT key offset
   # 获取所有(一个或多个)给定 key 的值
   MGET key1 [key2..]
   # 对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)
   SETBIT key offset value
   # 将值 value 关联到 key ，并将 key 的过期时间设为 seconds (以秒为单位)
   SETEX key seconds value
   # 只有在 key 不存在时设置 key 的值
   SETNX key value
   # 用 value 参数覆写给定 key 所储存的字符串值，从偏移量 offset 开始
   SETRANGE key offset value
   # 返回 key 所储存的字符串值的长度
   STRLEN key
   # 同时设置一个或多个 key-value 对
   MSET key value [key value ...]
   # 同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在
   MSETNX key value [key value ...]
   # 这个命令和 SETEX 命令相似，但它以毫秒为单位设置 key 的生存时间
   PSETEX key milliseconds value
   # 将 key 中储存的数字值增一
   INCR key
   # 将 key 所储存的值加上给定的增量值（increment）
   INCRBY key increment
   # 将 key 所储存的值加上给定的增量值（increment）
   INCRBYFLOAT key increment
   # 将 key 中储存的数字值减一
   DECR key
   # key 所储存的值减去给定的减量值（decrement）
   DECRBY key decrement
   # 如果 key 已经存在并且是一个字符串， APPEND 命令将指定的 value 追加到该 key 原来值的末尾
   APPEND key value
   ```

2. List

   数据编码:

   - REDIS_ENCODING_ZIPLIST

     数据结构: 压缩列表

     条件: 

     1. list对象保存的所有字符串元素的长度都小于64字节
     2. list对象保存的元素数量小于512个，

   - REDIS_ENCODING_LINKEDLIST

     数据结构: 双向链表

     条件: 其他

   命令：

   ```bash
   # 移出并获取列表的第一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止
   BLPOP key1 [key2 ] timeout
   # 移出并获取列表的最后一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止
   BRPOP key1 [key2 ] timeout
   # 从列表中弹出一个值，将弹出的元素插入到另外一个列表中并返回它； 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。
   BRPOPLPUSH source destination timeout
   # 通过索引获取列表中的元素
   LINDEX key index
   # 在列表的元素前或者后插入元素
   LINSERT key BEFORE|AFTER pivot value
   # 获取列表长度
   LLEN key
   # 移出并获取列表的第一个元素
   LPOP key
   # 将一个或多个值插入到列表头部
   LPUSH key value1 [value2]
   # 将一个值插入到已存在的列表头部
   LPUSHX key value
   # 获取列表指定范围内的元素
   LRANGE key start stop
   # 移除列表元素
   LREM key count value
   # 移除列表的最后一个元素，返回值为移除的元素
   RPOP key
   # 移除列表的最后一个元素，并将该元素添加到另一个列表并返回
   RPOPLPUSH source destination
   # 在列表中添加一个或多个值
   RPUSH key value1 [value2]
   # 为已存在的列表添加值
   RPUSHX key value
   ```

3. Hash

   数据编码:

   - REDIS_ENCODING_ZIPLIST

     数据结构: 压缩列表

     条件:

     1. list对象保存的所有字符串元素的长度都小于64字节
     2. list对象保存的元素数量小于512个

   - REDIS_ENCODING_HT

     数据结构: 哈希表

     条件: 其他

   命令:

   ```bash
   # 删除一个或多个哈希表字段
   HDEL key field1 [field2]
   # 查看哈希表 key 中，指定的字段是否存在。
   HEXISTS key field
   # 获取存储在哈希表中指定字段的值
   HGET key field
   # 获取在哈希表中指定 key 的所有字段和值
   HGETALL key
   # 为哈希表 key 中的指定字段的整数值加上增量 increment 
   HINCRBY key field increment
   # 为哈希表 key 中的指定字段的浮点数值加上增量 increment
   HINCRBYFLOAT key field increment
   # 获取所有哈希表中的字段
   HKEYS key
   # 获取哈希表中字段的数量
   HLEN key
   # 获取所有给定字段的值
   HMGET key field1 [field2]
   # 同时将多个 field-value (域-值)对设置到哈希表 key 中
   HMSET key field1 value1 [field2 value2 ]
   # 将哈希表 key 中的字段 field 的值设为 value
   HSET key field value
   # 只有在字段 field 不存在时，设置哈希表字段的值
   HSETNX key field value
   # 获取哈希表中所有值
   HVALS key
   # 迭代哈希表中的键值对
   HSCAN key cursor [MATCH pattern] [COUNT count]
   ```

4. Set 

   数据编码:

   - REDIS_ENCODING_INTSET

     数据结构：整数集合

     条件: 

     1. set对象保存的所有元素都是整数值
     2. set对象保存的元素数量不超过512个

   - EDIS_ENCODING_HT

     数据结构: 哈希表

     条件: 其他

   命令:

   ```bash
   # 向集合添加一个或多个成员
   SADD key member1 [member2]
   # 获取集合的成员数
   SCARD key
   # 返回第一个集合与其他集合之间的差异
   SDIFF key1 [key2]
   # 返回给定所有集合的差集并存储在 destination 中
   SDIFFSTORE destination key1 [key2]
   # 返回给定所有集合的交集
   SINTER key1 [key2]
   # 返回给定所有集合的交集并存储在 destination 中
   SINTERSTORE destination key1 [key2]
   # 判断 member 元素是否是集合 key 的成员
   SISMEMBER key member
   # 返回集合中的所有成员
   SMEMBERS key
   # 将 member 元素从 source 集合移动到 destination 集合
   SMOVE source destination member
   # 移除并返回集合中的一个随机元素
   SPOP key
   # 返回集合中一个或多个随机数
   SRANDMEMBER key [count]
   # 移除集合中一个或多个成员
   SREM key member1 [member2]
   # 返回所有给定集合的并集
   SUNION key1 [key2]
   # 所有给定集合的并集存储在 destination 集合中
   SUNIONSTORE destination key1 [key2]
   # 迭代集合中的元素
   SSCAN key cursor [MATCH pattern] [COUNT count]
   ```

5. Sorted set

   数据编码:

   - REDIS_ENCODING_ZIPLIST

     数据结构: 压缩列表

     条件: 

     1. 有序集合保存的元素数量小于128个
     2. 有序集合保存的所有元素的长度都小于64字节

   - REDIS_ENCODING_SKIPLIST

     数据结构: 跳表

     条件: 其他

   命令：

   ```bash
   # 向有序集合添加一个或多个成员，或者更新已存在成员的分数
   ZADD key score1 member1 [score2 member2]
   # 获取有序集合的成员数
   ZCARD key
   # 计算在有序集合中指定区间分数的成员数
   ZCOUNT key min max
   # 有序集合中对指定成员的分数加上增量 increment
   ZINCRBY key increment member
   # 计算给定的一个或多个有序集的交集并将结果集存储在新的有序集合 destination 中
   ZINTERSTORE destination numkeys key [key ...]
   # 在有序集合中计算指定字典区间内成员数量
   ZLEXCOUNT key min max
   # 通过索引区间返回有序集合指定区间内的成员
   ZRANGE key start stop [WITHSCORES]
   # 通过字典区间返回有序集合的成员
   ZRANGEBYLEX key min max [LIMIT offset count]
   # 通过分数返回有序集合指定区间内的成员
   ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT]
   # 返回有序集合中指定成员的索引
   ZRANK key member
   # 移除有序集合中的一个或多个成员
   ZREM key member [member ...]
   # 移除有序集合中给定的字典区间的所有成员
   ZREMRANGEBYLEX key min max
   # 移除有序集合中给定的排名区间的所有成员
   ZREMRANGEBYRANK key start stop
   # 移除有序集合中给定的分数区间的所有成员
   ZREMRANGEBYSCORE key min max
   # 返回有序集中指定区间内的成员，通过索引，分数从高到低
   ZREVRANGE key start stop [WITHSCORES]
   # 返回有序集中指定分数区间内的成员，分数从高到低排序
   ZREVRANGEBYSCORE key max min [WITHSCORES]
   # 返回有序集合中指定成员的排名，有序集成员按分数值递减(从大到小)排序
   ZREVRANK key member
   # 返回有序集中，成员的分数值
   ZSCORE key member
   # 计算给定的一个或多个有序集的并集，并存储在新的 key 中
   ZUNIONSTORE destination numkeys key [key ...]
   # 迭代有序集合中的元素（包括元素成员和元素分值）
   ZSCAN key cursor [MATCH pattern] [COUNT count]
   ```



### 全局哈希表是如何进行rehash的

关键词: ***两个全局哈希表*** ***渐近性rehash***

回答:

1. Redis 默认使用了两个全局哈希表：ht[0] 和 ht[1], 让字典同时持有ht[0] 和 ht[1]两个哈希表，为ht[1] 分配空间
2. 在字典中维持一个索引计数器变量rehashidx，并将其的值设为0，表示rehash工作正式开始
3. 在rehash进行期间，每次字典执行增加删除查找更新操作时，程序除了执行制定的操作以外，还会将ht[0]哈希表在rehashidx索引上的所有键值对rehash到ht[1],当rehash工作完成之后，程序将rehashidx属性的值+1
4. 随着字典操作的不断执行，最终在某个时间点，ht[0]所有的键值对都会被rehash到ht[1]，这时程序将rehashidx属性设为-1，表示rehash已完成。

好处是：将rehash键值所需计算的工作量均摊到字典的每个操作上，避免了集中式rehash带来的庞大计算量