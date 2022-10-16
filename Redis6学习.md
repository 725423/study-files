# Redis6学习



## NoSQL数据库简介

#### 技术发展：

技术的分类：

1.解决功能性的问题：Java、Jsp、RDBMS、Tomcat、HTML、Linux、JDBC、SVN

2.解决扩展性的问题：Struts、Spring、SpringMVC、Hibernate、Mybatis

3.解决性能的问题：NoSQL、Java线程、Hadoop、Nginx、MQ、ElasticSearch（ES）



#### NoSQL数据库简介（Not Only SQL）泛指非关系型数据库

NoSQL不依赖业务逻辑方式存储，而以简单的key-value模式存储，因此大大增加了数据库的扩展能力。

1.不遵循SQL标准

2.不支持ACID

3.远超于SQL的性能



#### NoSQL使用场景

1.对数据高并发的读写

2.海量数据读写

3.对数据高可扩展性



#### NoSQL不适用场景

1.需要事务支持

2.基于sql的结构化查询存储，处理复杂的关系，需要即席查询

3.用不着sql的和用了sql也不行的情况，请考虑用NoSQL



## reids下载安装

下载redis安装包，通过xftp连接虚拟机，（需要先安装gcc）将安装包传输到虚拟机中放入/opt目录，然后解压

解压命令：tar -zxvf redis...

完成后进入目录cd redis..     在此目录下执行make命令（只是编译好），编译成功后用make install 命令进行安装

安装后默认在/usr/local/bin目录下

#### 查看默认的安装目录

redis-benchmark：性能测试工具

redis-check-aof：修复有问题的AOF文件

redis-check-dump：修复有问题的dump.rdb文件

redis-sentinel：Redis集群使用

redis-server：Redis服务器启动命令

#### 推荐使用后台启动（好处：启动窗口关闭之后还在启动状态）

- 备份redis.conf

- 拷贝一份redis.conf到其他目录

- cp /opt/redis../redis.conf /etc/redis.comf

- 后台启动设置daemonize no 改为yes

- 修改redis.conf文件将里面的daemonize no改为yes，让服务器在后台启动

- Redis启动

  启动命令：先进入/usr/local/bin目录        redis-server /etc/redis.conf

- 检测是否启动：ps -ef | grep redis

- 用客户端访问：redis-cli

- 多个端口可以：redis-cli -p6379

- 测试验证：ping，结果显示：PONG

- 用shutdown进行关闭



## Redis介绍相关知识

#### 数据库的介绍

- 默认16个数据库，下标从0开始，初始默认使用0号库
- 使用select <dbid>来切换数据库，如select 8，统一密码管理，所有库同样密码
- dbsize：查看当前数据库的key数量
- flushdb：清空当前库
- flushall：通杀全部库

#### Redis是单线程+多路IO复用技术

- 多路复用是指使用一个线程来检查多个文件描述符（Socket）的就绪状态，比如调用select和poll函数，传入多个文件描述符，如果有一个文件描述符就绪，则返回，否则阻塞直到超时。得到就绪状态后进行真正的操作可以再同一个线程里执行，也可以启动线程执行（比如使用线程池）
- 串行   vs    多线程+锁（memcache）   vs    单线程+多路IO复用（Redis）
- 与Memcache三点不同：支持多数据类型、支持持久化、单线程+多路IO复用





## Springboot整合Redis

- 导入依赖：

- ```xml
  <!--redis-->
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-redis</artifactId>
      <version>2.6.7</version>
  </dependency>
  
  <!--spring2.x 集成redis所需commons-pool2-->
  <dependency>
      <groupId>org.apache.commons</groupId>
      <artifactId>commons-pool2</artifactId>
      <version>2.11.1</version>
  </dependency>
  ```

- 配置：使用properties或者yml

- ```properties
  # REDIS (RedisProperties)
  # Redis数据库索引（默认为0）
  spring.redis.database=0
  # Redis服务器地址
  spring.redis.host=192.168.182.129
  # Redis服务器连接端口
  spring.redis.port=6379
  # Redis服务器连接密码（默认为空）
  spring.redis.password=
  # 连接池最大连接数（使用负值表示没有限制）
  spring.redis.jedis.pool.max-active=8
  # 连接池最大阻塞等待时间（使用负值表示没有限制）
  spring.redis.jedis.pool.max-wait=-1
  # 连接池中的最大空闲连接
  spring.redis.jedis.pool.max-idle=8
  # 连接池中的最小空闲连接
  spring.redis.jedis.pool.min-idle=0
  # 连接超时时间（毫秒）
  spring.redis.timeout=5000
  ```

- yml：

- ```yaml
  redis:
    #数据库索引
    database: 0
    host: 192.168.182.129
    port: 6379
    password:
    jedis:
      pool:
        #最大连接数
        max-active: 8
        #最大阻塞等待时间(负数表示没限制)
        max-wait: -1
        #最大空闲
        max-idle: 8
        #最小空闲
        min-idle: 0
        #连接超时时间
    timeout: 10000
  ```

- 配置类：RedisConfig

- 

- ```java
  package cn.zb.redis_springboot.config;
  
  import org.springframework.cache.annotation.CachingConfigurerSupport;
  import org.springframework.cache.annotation.EnableCaching;
  import org.springframework.context.annotation.Bean;
  import org.springframework.context.annotation.Configuration;
  import org.springframework.data.redis.connection.RedisConnectionFactory;
  import org.springframework.data.redis.core.RedisTemplate;
  import org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer;
  import org.springframework.data.redis.serializer.RedisSerializer;
  import org.springframework.data.redis.serializer.StringRedisSerializer;
  
  @Configuration
  @EnableCaching
  public class RedisConfig extends CachingConfigurerSupport {
      @Bean(name = "redisTemplate")
      public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory){
  
          RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
          //参照StringRedisTemplate内部实现指定序列化器
          redisTemplate.setConnectionFactory(redisConnectionFactory);
          redisTemplate.setKeySerializer(keySerializer());
          redisTemplate.setHashKeySerializer(keySerializer());
          redisTemplate.setValueSerializer(valueSerializer());
          redisTemplate.setHashValueSerializer(valueSerializer());
          return redisTemplate;
      }
  
      private RedisSerializer<String> keySerializer(){
          return new StringRedisSerializer();
      }
  
      //使用Jackson序列化器
      private RedisSerializer<Object> valueSerializer(){
          return new GenericJackson2JsonRedisSerializer();
      }
  
  }
  ```

## 事务和锁机制（基本操作）

- multi：开启事务，进入组队阶段，开始设置值（set xx， set xx...）
- exec：执行阶段
- discard：放弃组队，在组队阶段之后
- Redis事务是一个单独隔离操作：事务中的所有命令都会序列化、按顺序的执行。事务在执行过程中，不会被其他客户端发送来的命令请求所打断

#### 悲观锁

每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会block直到它拿到锁。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁、表锁等，读锁，写锁等，都是再做操作之前先上锁。（缺点：效率很低）

#### 乐观锁

每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。乐观锁适用于多读的应用类型，这样可以提高吞吐量。Redis就是利用这种check-and-set机制实现事务的

#### watch

在执行multi之前，先执行watch key1 [key2]，可以监视一个（或多个）key，如果在事务执行之前这个（或这些）key被其他命令所改动，那么事务将被打断

#### Redis事务三特性

- 单独的隔离操作：事务中的所有命令都会序列化。按顺序的执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断
- 没有隔离级的概念：队列中的命令没有提交之前都不会被实际执行，因为事务提交之前任何指令都不会被实际执行
- 不保证原子性：事务中如果有一条命令执行失败，其后的命令仍然会被执行，没有回滚

#### Redis简介

- Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用。
- Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。
- Redis支持数据的备份，即master-slave模式的数据备份。

#### Redis数据类型

​		String（字符串）、hash（哈希）、list（列表）、set（集合）及zset（sorted set：有序集合）

#### Redis常见keys命令

- del key ：该命令用于在key存在时删除key
- dump key ：序列化给定 key ，并返回被序列化的值
- exists key ：检查给定key是否存在
- expire key seconds ：为key设置过期时间，以秒计
- expire key milliseconds ：设置key过期时间，以毫秒记
- keys pattern ：查找所有符合给定模式（pattern）的key
- move key db ：将当前数据库的 key 移动到给定的数据库 db 当中
- persist key ：移除key的过期时间，key将持久保持
- pttl key ：以毫秒为单位返回key的剩余过期时间
- randomkey ：从当前数据库中随即返回一个key
- rename key newkey ：修改key的名称
- renamenx key newkey ：仅当newkey不存在时，将key改为newkey
- type key ：返回key所存储值的类型

#### Redis字符串命令

- set key value ：设置指定key的值
- get key ：获取指定key的值
- getrange key start end ：返回key中字符串的子字符串，从0开始
- getset key value ：将给定key的值设为value，并返回key的旧值（即修改）
- getbit key offset ：对key所存储的字符串值，获取指定偏移量上的位（bit）
- mget key1[key2...] ：获取所有(一个或多个)给定 key 的值
- setbit key offset value ：对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)
- setex key seconds value ：将值 value 关联到 key ，并将 key 的过期时间设为 seconds (以秒为单位)
- setex key value ：只有在key不存在时设置key的值
- setrange key offset value ：用 value 参数覆写给定 key 所储存的字符串值，从偏移量 offset 开始（覆盖指定位置开始的值）
- strlen key ：返回key所存储字符串值的长度
- mset key value[key value...] ：同时设置一个或多个key-value键值对
- msetex key value[key value...]  ：同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在
- psetex key milliseconds value ：这个命令和 SETEX 命令相似，但它以毫秒为单位设置 key 的生存时间，而不是像 SETEX 命令那样，以秒为单位
- incr key ：将key中存储的数字增加1
- incrby key increment ：将 key 所储存的值加上给定的增量值（increment）
- incrbyfloat key increment ：将 key 所储存的值加上给定的浮点增量值（increment）
- decr key ：将 key 中储存的数字值减一
- decrby key decrment ：key 所储存的值减去给定的减量值（decrement）
- append key value ：如果 key 已经存在并且是一个字符串， APPEND 命令将指定的 value 追加到该 key 原来值（value）的末尾

#### Redis hash 命令

- hdel key field1 [field2] ：删除一个或多个哈希表字段
- hexists key field ：查看哈希表 key 中，指定的字段是否存在
- hget key field ：获取存储在哈希表中指定字段的值
- hgetall key ：获取在哈希表中指定 key 的所有字段和值
- hincrby key field increment ：为哈希表 key 中的指定字段的整数值加上增量 increment 
- hincrbyfloat key field increment ：为哈希表 key 中的指定字段的浮点数值加上增量 increment
- hkeys key ：获取所有哈希表中的字段
- hlen key ：获取哈希表中字段的数量
- hmget key field1[field2] ：获取所有给定字段的值
- hmset key field value ：将哈希表 key 中的字段 field 的值设为 value
- hmset key field1 value1 [field2 value1] ：同时将多个 field-value (域-值)对设置到哈希表 key 中
- hsetnx key field value ：只有在字段 field 不存在时，设置哈希表字段的值
- hvals key ：获取哈希表中所有值
- hscan key cursor [match pattern] [count count] ：迭代哈希表中的键值对

#### Redis 列表命令

- blpop key1 [key2] timeout ：移出并获取列表的第一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止

- brpop key1 [key2] timeout ：移出并获取列表的最后一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止

- brpoplpush source destination timeout ：从列表中弹出一个值，将弹出的元素插入到另外一个列表中并返回它； 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止

- lindex key index ：通过索引获取列表中的元素

- linsert key before|after pivot value ：在列表的元素前或者后插入元素

- llen key ：获取列表长度

- lpop key ：移出并获取列表的第一个元素

- lpush key value1 [value2] ：将一个或多个值插入到列表头部

- lpushx key value ：将一个值插入到已存在的列表头部

- lrange key start stop ：获取列表指定范围内的元素

- lrem key count value ：移除列表元素

  ------

  count > 0 : 从表头开始向表尾搜索，移除与 VALUE 相等的元素，数量为 COUNT 。

  count < 0 : 从表尾开始向表头搜索，移除与 VALUE 相等的元素，数量为 COUNT 的绝对值。

  count = 0 : 移除表中所有与 VALUE 相等的值

  ------

  

- lset key index value ：通过索引设置列表元素的值

- ltrim key start stop ：对一个列表进行修剪(trim)，就是说，让列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除

- rpop key ：移除列表的最后一个元素，返回值为移除的元素

- rpoplpush source destination ：移除列表的最后一个元素，并将该元素添加到另一个列表并返回

- rpush key value1 [value2] ：在列表中添加一个或多个值

- rpushx key value ：为已存在的列表添加值

#### Redis 集合命令

- sadd key member1 [member2] ：向集合添加一个或多个成员
- scard key ：获取集合的成员数
- sdiff key1 [key2] ：返回第一个集合与其他集合之间的差异
- sdiffstore destination key1 [key2] ：返回给定所有集合的差集并存储在 destination 中
- sinter key1 [key2] ：返回给定所有集合的交集
- sinterstore destination key1 [key2] ：返回给定所有集合的交集并存储在 destination 中
-  sismember key member ：判断 member 元素是否是集合 key 的成员
- smembers key ：返回集合中的所有成员
- smove source destination member ：将 member 元素从 source 集合移动到 destination 集合
- spop key ：移除并返回集合中的一个随机元素
- srandmember key [count] ：返回集合中一个或多个随机数
- srem key member1 [member2] ：移除集合中一个或多个成员
- sunion key1 [key2] ：返回所有给定集合的并集
- sunionstore destination key1 [key2] ：所有给定集合的并集存储在 destination 集合中
- sscan key cursor [match pattern] [count count] ：迭代集合中的元素

#### Redis 有序集合命令

- zadd key score1 member1 [score2 member2] ：向有序集合添加一个或多个成员，或者更新已存在成员的分数
- zcard key ：获取有序集合的成员数
- zcount key min max ：计算在有序集合中指定区间分数的成员数
- zincrby key increment member ：有序集合中对指定成员的分数加上增量 increment
- zinterstore destination numkeys key [key...] ：计算给定的一个或多个有序集的交集并将结果集存储在新的有序集合 destination 中
- zlexcount key min max ：在有序集合中计算指定字典区间内成员数量
- zrange key start stop [withscores] ：通过索引区间返回有序集合指定区间内的成员
- zrangebylex key min max [limit offset count] ：通过字典区间返回有序集合的成员
- zrangebyscore key min max [withscores] [limit] ：通过分数返回有序集合指定区间内的成员
- zrank key member ：返回有序集合中指定成员的索引
- zrem key member [member] ：移除有序集合中的一个或多个成员
- zremrangebylex key min max ：移除有序集合中给定的字典区间的所有成员
- zremrangebyrank key start stop ：移除有序集合中给定的排名区间的所有成员
- zremrangebyscore key min max ：移除有序集合中给定的分数区间的所有成员
- zrevrange key start stop [withscores] ：返回有序集中指定区间内的成员，通过索引，分数从高到低
- zrevrangebyscore key max min [withscores] ：返回有序集中指定分数区间内的成员，分数从高到低排序
- zrevrank key member ：返回有序集合中指定成员的排名，有序集成员按分数值递减(从大到小)排序
- zscore key member ：返回有序集中，成员的分数值
- zunionstore destination numkeys key [key...] ：计算给定的一个或多个有序集的并集，并存储在新的 key 中
- zscan key cursor [match pattern] [count count] ：迭代有序集合中的元素（包括元素成员和元素分值）

#### Redis HyperLogLog

​	**Redis HyperLogLog 的作用**

​		Redis HyperLogLog 是用来做基数统计的算法，HyperLogLog 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定 的、并且是很小的

​		在 Redis 里面，每个 HyperLogLog 键只需要花费 12 KB 内存，就可以计算接近 2^64 个不同元素的基 数。这和计算基数时，元素越多耗费内存就越多的集合形成鲜明对比，但是，因为 HyperLogLog 只会根据输入元素来计算基数，而不会储存输入元素本身，所以 HyperLogLog 不能像集合那样，返回输入的各个元素

​	**什么是基数**

​		比如数据集 {1, 3, 5, 7, 5, 7, 8}， 那么这个数据集的基数集为 {1, 3, 5 ,7, 8}, 基数(不重复元素)为5。 基数估计就是在误差可接受的范围内，快速计算基数

​	Redis HyperLogLog命令

- pfadd key element [element...] ：添加指定元素到 HyperLogLog 中
- pfcount key [key...] ：返回给定 HyperLogLog 的基数估算值
- pfmerge destkey sourcekey [sourcekey...] ：将多个 HyperLogLog 合并为一个 HyperLogLog 

#### Redis 发布订阅

![image-20220810100323723](C:\Users\22906\AppData\Roaming\Typora\typora-user-images\image-20220810100323723.png)

​	**Redis 发布订阅命令**

- subscribe channel [channel...] ：订阅给定的一个或多个频道的信息
- unsubscribe [channel [channel...]] ：指退订给定的频道
- psubscribe pattern [pattern...] ：订阅一个或多个符合给定模式的频道
- pubsub subcommand [argument [argument]] ：查看订阅与发布系统状态
- publish channel message ：将信息发送到指定频道
- punsubcribe [pattern [pattern...]] ：退订所有给定模式的频道

#### Redis 事务

​	**Redis 事务可以一次执行多个命令， 并且带有以下三个重要的保证**

- 批量操作在发送 EXEC 命令前被放入队列缓存
- 收到 EXEC 命令后进入事务执行，事务中任意命令执行失败，其余的命令依然被执行
- 在事务执行过程，其他客户端提交的命令请求不会插入到事务执行命令序列中

​	**一个事务从开始到执行会经历以下三个阶段**

- 开始事务
- 命令入列
- 执行事务

​	**Redis 事务命令**

- discard ：取消事务，放弃执行事务块内的所有命令
- exec ：执行所有事务块内的命令
- multi ：标记一个事务块的开始
- unwatch ：取消watch命令对所有key的监视
- watch key [key...] ：监视一个(或多个) key ，如果在事务执行之前这个(或这些) key 被其他命令所改动，那么事务将被打断

​	**总结**

- 单个 Redis 命令的执行是原子性的，但 Redis 没有在事务上增加任何维持原子性的机制，所以 Redis 事务的执行并不是原子性的
- 事务可以理解为一个打包的批量执行脚本，但批量指令并非原子化的操作，中间某条指令的失败不会导致前面已做指令的回滚，也不会造成后续的指令不做
- 一但执行 EXEC 开启事务的执行后，无论事务使用执行成功， WARCH 对变量的监控都将被取消
- 故当事务执行失败后，需重新执行WATCH命令对变量进行监控，并开启新的事务进行操作
- **watch指令类似于乐观锁，在事务提交时，如果watch监控的多个KEY中任何KEY的值已经被其他客户端更改，则使用EXEC执行事务时，事务队列将不会被执行，同时返回Nullmulti-bulk应答以通知调用者事务执行失败**

#### Redis 脚本命令

- eval script numkeys key [key...] arg [arg...] ：执行 Lua 脚本
- evalsha sha1 numkeys key [key...] arg [arg...] ：执行 Lua 脚本
- script exists script [script...] ：查看指定的脚本是否已经被保存在缓存当中
- script flush ：从脚本缓存中移除所有脚本
- script kill ：杀死当前正在运行的 Lua 脚本
- script load scipt ：将脚本 script 添加到脚本缓存中，但并不立即执行这个脚本

#### Redis 连接命令

- auth password ：验证密码是否正确
- echo message ：打印字符串
- ping ：查看服务是否运行
- quit ：关闭当前连接
- select index ：切换到指定的数据库



### 出现中文乱码时

- 连接时使用：redis-cli --raw







