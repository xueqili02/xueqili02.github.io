---
title: Redis Note
date: 2023-11-30 10:30:12
# tags: [Hexo, Keep]
# categories: Introduction
---

# Redis Note

- [Redis Note](#redis-note)
  - [Redis简介](#redis简介)
  - [SQL和NoSQL对比。](#sql和nosql对比)
  - [Redis特征](#redis特征)
  - [Redis数据结构和类型](#redis数据结构和类型)
    - [String类型](#string类型)
    - [KEY的层级结构](#key的层级结构)
    - [Hash类型](#hash类型)
    - [List类型](#list类型)
    - [Set类型](#set类型)
    - [SortedSet](#sortedset)
  - [Redis的Java客户端](#redis的java客户端)
    - [Jedis](#jedis)
    - [Jedis连接池](#jedis连接池)
    - [SpringDataRedis](#springdataredis)
  - [缓存](#缓存)
    - [添加缓存](#添加缓存)
    - [缓存策略](#缓存策略)
  - [优惠券秒杀](#优惠券秒杀)
    - [全局唯一ID](#全局唯一id)
    - [库存超卖问题](#库存超卖问题)
    - [一人一单](#一人一单)
    - [分布式锁](#分布式锁)
      - [问题1：误删除锁](#问题1误删除锁)
      - [问题2：删除锁操作的原子性](#问题2删除锁操作的原子性)
      - [Redis的Lua脚本 解决问题2](#redis的lua脚本-解决问题2)
    - [分布式锁的优化](#分布式锁的优化)
      - [基于SETNX实现的分布式锁存在下列问题：](#基于setnx实现的分布式锁存在下列问题)
      - [Redisson](#redisson)
  - [Redis分布式缓存](#redis分布式缓存)
    - [Redis 持久化](#redis-持久化)
    - [Redis 主从](#redis-主从)
    - [Redis 哨兵](#redis-哨兵)
    - [Redis 分片集群](#redis-分片集群)
      - [散列插槽](#散列插槽)
      - [集群伸缩](#集群伸缩)
      - [故障转移](#故障转移)
  - [多级缓存](#多级缓存)
  - [Redis 最佳实践](#redis-最佳实践)
    - [BigKey](#bigkey)
    - [恰当的数据类型](#恰当的数据类型)
    - [批处理](#批处理)
      - [pipeline](#pipeline)
      - [集群下的批处理](#集群下的批处理)
    - [服务端优化](#服务端优化)
      - [持久化配置](#持久化配置)
      - [慢查询](#慢查询)
      - [命令及安全配置](#命令及安全配置)
      - [内存配置](#内存配置)
      - [集群配置](#集群配置)
  - [Redis 数据结构](#redis-数据结构)
    - [动态字符串 SDS](#动态字符串-sds)
    - [IntSet](#intset)
    - [字典 Dict](#字典-dict)
    - [ZipList](#ziplist)
    - [QuickList](#quicklist)
    - [跳表 SkipList](#跳表-skiplist)
    - [RedisObject](#redisobject)
  - [Redis 数据类型](#redis-数据类型)
    - [String](#string)
    - [List](#list)
    - [Set](#set)
    - [ZSET](#zset)
    - [Hash](#hash)
  - [Redis 网络模型](#redis-网络模型)
    - [用户空间和内核空间](#用户空间和内核空间)
    - [阻塞IO](#阻塞io)
    - [非阻塞IO](#非阻塞io)
    - [IO多路复用](#io多路复用)
      - [select模式](#select模式)
      - [poll模式](#poll模式)
      - [epoll](#epoll)
      - [事件通知机制](#事件通知机制)
    - [信号驱动IO](#信号驱动io)
    - [异步IO](#异步io)
    - [Redis网络模型](#redis网络模型)
  - [Redis 通信协议](#redis-通信协议)
    - [RESP协议 Redis Serialization Protocol](#resp协议-redis-serialization-protocol)
  - [Redis 内存回收](#redis-内存回收)
    - [过期策略](#过期策略)
    - [淘汰策略](#淘汰策略)


## Redis简介

Redis，Remote Dictionary Server，远程词典服务器，是一个基于内存的键值对NoSQL数据库。

## SQL和NoSQL对比。

- SQL structured query language. 
    1. 它是结构化的，表结构不轻易改变。
    2. 表之间有关联。
    3. 需要通过SQL查询。
    4. 满足ACID。
    5. 存储在磁盘
    6. 垂直扩展
- NoSQL
    1. 是非结构化，常见类型有key-value型、Document型、图类型、列类型等。
    2. 数据之间没有关联。
    3. 可直接使用get查询。
    4. 基本的一致性。
    5. 存储在内存
    6. 水平扩展

## Redis特征
- 键值型，value支持多种不同数据结构
- 单线程，每个命令具有原子性（Redis 6后处理网络请求支持多线程，但命令处理本质还是单线程）
- 低延迟，速度快（基于内存、IO多路复用、良好的编码）
- 支持数据持久化
- 支持主从集群、分片集群
- 支持多语言客户端

## Redis数据结构和类型
- string
- hash
- list
- set
- sortedset
- GEO
- BitMap
- HyperLog

通用命令：
- KEYS：查看符合模板的所有key，不建议在生产环境设备上使用。
- DEL：删除一个指定的KEY，可跟多个
- MSET：设置多个kv。
- EXISTS：判断key是否存在
- EXPIRE：给一个key设置有效期，有效期到期该key会被自动删除
- TTL：查看一个key的剩余有效期（返回-1表示无有效期，返回-2表示已过期被删除）

### String类型
字符串类型，value是字符串，可以分为三类：string、int、float。底层都是字节数组形式存储，编码方式不同，如：数值类型会被转为二进制存储。

常见命令：
- SET：添加kv或修改已存在kv的value
- GET：根据key获取String类型的value
- MSET：批量添加多个String类型kv
- MGET：根据多个key获取多个String类型的value
- INCR：使整型的val自增1
- INCRBY：使整型key自增并指定步长
- INCRBYFLOAT：浮点类型的数字自增并指定步长
- SETNX：添加String类型键值对，前提是key不存在，否则不添加
- SETEX：添加String类型kv，指定有效期

### KEY的层级结构
Redis的KEY允许多个单词形成层级结构，单词之间用':'隔开，格式例如  项目名:业务名:类型:id

### Hash类型
value是一个无序字典，类似于HashMap。

Hash结构，可以将对象中的每个字段独立存储，可针对单个字段进行CRUD。

KEY + VALUE, 每个VALUE都是field + value

常见命令：
- HSET key field value：添加或修改hash类型key的field的值
- HGET key field：获取
- HMSET：批量设置
- HMGET：批量获取
- HGETALL：获取hash的key中的所有的field和value
- HKEYS：获取hash类型的key中的所有field
- HVALS：获取hash类型的key中的所有value
- HINCRBY key field increment：使hash类型key的字段值自增并指定步长
- HSETNX：添加hash类型的key的field值，前提是field不存在，否则不执行

### List类型
与Java中LinkedList类似，双向链表，支持正向和反向检索。

特征：
- 有序
- 元素可重复
- 插入和删除快
- 查询速度一般

可用于朋友圈点赞评论列表

常见命令：
- LPUSH key element ... ：列表左侧插入一或多个元素
- LPOP key：列表左侧元素移除并返回，没有则返回nil
- RPUSH key element ... ：右
- RPOP key：右
- LRANGE key start end：返回start到end范围内的元素，start和end是0开始的下表，0是list中的第一个元素，1是第二个，-1是最后一个，-2是倒数第二个
- BLPOP和BRPOP：与LPOP和RPOP类似，不过在没有元素时等待指定时间，而不是直接返回nil

### Set类型
与Java中的HashSet类似，特征为：
- 无序
- 元素不可重复
- 查找快
- 支持交集、并集、差集等功能

常见命令：
- SADD key member ...：添加一或多个元素
- SREM key member ...：移除指定元素
- SCARD key：返回set中元素的个数
- SISMEMBER key member：判断元素是否在set中
- SMEMBERS key：获取set中所有元素
- SINTER key1 key2：求key1与key2的交集
- SDIFF key1 key2：求差集
- SUNION key1 key2：求key1和key2的并集

### SortedSet
一个可排序的set集合，底层每个元素都带有score属性，基于score对元素排序，实现是跳表+hash表。具备以下特性：
- 可排序
- 元素不重复
- 查询速度快

因为SortedSet可排序，常被用来实现排行榜功能

常见命令：
- ZADD key score member：添加一个或多个元素，若已存在则更新score值
- ZREM key member：删除
- ZSCORE key member：查询score
- ZRANK key member：获取指定元素的排名
- ZCARD key：获取元素个数
- ZCOUNT key min max：统计score值在范围内的所有元素个数
- ZINCRBY key increment member：自增指定步长
- ZRANGE key min max：获取指定排名范围内的元素
- ZRANGEBYSCORE key min max：获取score范围内的元素
- ZDIFF、ZINTER、ZUNION：差交并集
  
所有排名默认升序，降序则在Z后添加REV，例如ZREV

## Redis的Java客户端

Jedis，Jedis实例线程不安全，多线程环境下需要基于连接池来使用。

Lettuce是基于Netty实现的，支持同步、异步和响应式编程，线程安全。支持Redis的哨兵模式、集群模式、管道模式。

Redisson基于Redis实现的分布式、可伸缩的Java数据结构集合，例如Map、Queue、Lock、Semaphore、AtomicLong。

### Jedis

1. 引入依赖
2. 创建对象，设置url和端口
3. 调用方法，方法名与redis相同
4. 释放资源，调用close()

### Jedis连接池

Jedis本身线程不安全，且频繁创建和销毁对象会造成很大的性能损耗，因此使用Jedis连接池。

### SpringDataRedis
使用步骤：
1. 引入spring-boot-starter-data-redis和common-pool2依赖
2. 在applicatoin.properties中添加参数
3. 注入RedisTemplate

RedisTemplate可以接收任意类型Object写入Redis，但是底层会使用ObjectOutputStream将Object序列化为字节，缺点是消耗内存、可读性差。

（不推荐）自定义RedisTemplate，使用JSON序列化。为了在反序列化时知道对象的类型，会将class类型写入JSON，内存开销较大

（推荐）使用StringRedisTemplate，写入Redis时，手动把对象序列化为JSON；读取时，手动把JSON反序列化为对象

## 缓存

缓存是数据交换的缓冲区，是存贮数据的临时区域，一般读写性能较高

缓存的作用
- 降低后端负载
- 提高读写效率，降低响应时间

缓存的缺点
- 数据一致性成本
- 代码维护成本
- 运维成本

### 添加缓存
1. 查询缓存
2. 命中，直接返回
3. 未命中，查询数据库
4. 数据库中不存在，返回错误
5. 数据库中存在，将数据存到redis中
6. 返回数据

### 缓存策略
- 内存淘汰
  - 不需要自己维护，利用Redis的内存淘汰机制，经过一段时间自动删除，下次查询时更新缓存
  - 一致性：差
  - 维护成本：无
- 超时剔除
  - 给缓存数据设置TTL，到期后自动删除，下次查询时更新
  - 一致性：一般
  - 维护成本：低
- 主动更新
  - 编写业务逻辑，更新数据库数据时，主动更新Redis缓存
  - 一致性：高
  - 维护成本：高
  - 策略1：Cache Aside Pattern，由缓存的调用者，在更新数据库时同步更新缓存
  - 策略2：Read/Write Through Pattern，将缓存和数据库整合为一个服务，由服务维护一致性。调用者只需调用，无需关心一致性问题
  - 策略3：Write Behind Caching Pattern，调用者只操作缓存，由其它线程异步将缓存数据持久化到数据库中，保证最终一致性

低一致性需求：使用内存淘汰，例如电影类型、店铺类型的查询

高一致性需求：使用主动更新，并用超时剔除保底，例如店铺详情、电影票信息的查询

我们选择Cache Aside Pattern
- 删除缓存还是更新缓存
  - 更新缓存：每次更新数据库都需要更新缓存，无效写操作较多。例如数据库多次更新，导致缓存多次更新，但是这段时间并没有请求访问缓存
  - **删除缓存：更新数据库时使缓存失效，查询时再更新缓存**
- 保存缓存与更新数据库的操作同时成功或失败
  - 单体系统：将缓存和数据库更新放在同一个事务
  - 分布式系统：考虑TCC等分布式事务方案
- 删除缓存+更新数据库策略，先操作缓存还是先操作数据库
  - 1 先删除缓存，再操作数据库：存在线程安全问题，发生概率较大
    - 理想情况：假设缓存和数据库中都是旧数据10
      - 第1步：线程1删除缓存
      - 第2步：线程1更新数据库，设置为20
      - 第3步：线程2读取缓存，未命中，访问数据库得到20
      - 第4步：线程2更新缓存为20
    - 线程不安全情况：假设缓存和数据库中都是旧数据10
      - 第1步：线程1删除缓存
      - 第2步：线程2查询缓存，未命中，查询数据库，得到旧数据10
      - 第3步：线程2写入缓存，旧数据10
      - 第4步：线程1更新数据库，设置为20
      - 此时数据库中是新数据20，缓存中是旧数据10，数据不一致
  - **2 先操作数据库，再删除缓存：存在线程安全问题，发生概率较小**
    - 理想情况：假设缓存和数据库中都是旧数据10
      - 第1步：线程1更新数据库，设置为20
      - 第2步：线程1删除缓存
      - 第3步：线程2查询缓存，未命中，查询数据库，查到20
      - 第4步：线程2写入缓存，新数据20
      - 此时数据库和缓存都是新数据20
    - 线程不安全情况：假设缓存和数据库中都是旧数据10
      - 第1步：线程1查询缓存，未命中，查询数据库，查到10
      - 第2步：线程2更新数据库，设置为20
      - 第3步：线程2删除缓存
      - 第4步：线程1写入缓存，缓存中是旧数据10
      - 此时数据库中是新数据20，缓存中是旧数据10，数据不一致
    - 这种不安全情况发生概率很低，因为它必须满足三个条件：
      - 缓存刚好失效
      - 读请求+写请求并发
      - 写数据库操作与删除缓存操作的时间，小于 读数据库与写缓存的时间（很难发生）

读操作
- 缓存命中直接返回
- 缓存未命中则查询数据库，并写入缓存，设置超时时间

写操作
- 先操作数据库，再删除缓存
- 要确保数据库与缓存操作的原子性

- 缓存穿透 cache penetration：请求的数据在缓存和数据库中都不存在，这样缓存永远不会生效，大量请求同时到达数据库
  - 缓存空对象，优点：实现简单、维护方便；缺点：额外的内存消耗、可能有短期的不一致
  - 布隆过滤：存储hash后的二进制bit。在请求访问缓存之前判断，如果判断对象不存在，则一定不存在；如果判断对象存在，则有一定可能不存在。优点：内存占用少；缺点：可能误判，实现复杂
  - 增强id的复杂性：雪花id
  - 进行数据的基础格式校验
- 缓存雪崩 cache avalanche：同一时段大量的缓存Key同时失效，或Redis服务宕机，导致大量请求全部到达数据库，带来巨大压力
  - 给不同的key的TTL添加随机值，防止同时失效
  - 利用Redis集群，提高可用性
  - 给缓存业务添加降级限流策略
  - 给业务添加多级缓存
- 缓存击穿 hotspot invalid：也叫热点Key问题，当一个被高并发访问并且缓存重建业务较复杂的key突然失效，大量请求会到达数据库
  - 互斥锁：内存占用小，保证一致性；会有线程等待，性能较差，且可能死锁
  - 逻辑过期：线程无需等待，性能好；不保证一致性，内存消耗大，实现复杂

## 优惠券秒杀

### 全局唯一ID

订单ID不能使用数据库自增ID
- 规律明显，暴露业务信息
- 受到单表数据量的限制

全局ID生成器，可在分布式系统下生成全局唯一的ID，一般要满足以下特性
- 唯一性
- 高可用
- 高性能
- 递增性
- 安全性

全局唯一ID：
- 符号位：1bit，永远为0
- 时间戳：31bit，以秒为单位，可以使用69年
- 序列号：32bit，秒内的计数器，支持每秒产生2^32个不同ID

在项目中，时间戳使用当前时间减去一个固定时间；序列号使用redis的string中的increment自增；拼接时，将时间戳左移32位，再与序列号进行或运算，得到ID

snowflake算法（雪花算法）
- 将64bit划分为不同部分，Java中的long类型就是64bit，因此snowflake算法生成的id使用long存储
  - 符号位：1bit，永远为0
  - 时间戳：31bit，以毫秒为单位，可以使用69年
  - 工作机器id：10bit，可表示1024台机器，
  - 自增序列号：12bit，可表示4096个数
  - 因此在一毫秒一个数据中心的一台机器上可产生4096个有序的不重复的ID
- 缺陷：雪花算法强依赖机器时钟，如果机器上时钟回拨，会导致发号重复或者服务会处于不可用状态。若回退前生成了一些id，回退后再生成id则可能造成重复
  - 解决方案：百度 UidGenerator，美团Leaf，Mist薄雾算法

### 库存超卖问题

判断逻辑：
1. 查询库存数量
2. 判断库存是否大于0
3. 下单扣减库存

理想情况：
- 线程1查询库存，剩余1
- 线程1判断库存大于0，扣减库存
- 线程2查询库存，剩余0
- 线程2判断库存小于等于0，无法下单

高并发情况
- 线程1查询库存，剩余1
- 线程2查询库存，剩余1
- 线程1扣减
- 线程2扣减
- 出现超卖情况

解决方案
- 悲观锁：认为一定会出现线程安全问题，因此在操作之前先获取锁，确保线程串行执行，例如使用synchronized或lock
- 乐观锁：认为线程安全问题不一定会发生，因此不加锁，只有在更新数据时判断有没有其他线程对数据进行更改
  - 如果未修改，则认为是安全的，自己更新
  - 如果被修改，则发生了安全问题，进行重试或异常
  - 版本号法：对记录添加一个version字段，每次更新数据前判断版本号是否与之前查询到的一致
  - CAS法：compare and set，在查询时记录库存个数，在更新前判断此时的库存个数是否与之前查询到的一致
    - 缺陷：失败率过高，因为大量线程可能同时查询了记录，但是只有一个线程可以更新成功
    - 解决办法：在update的where中设置`where stock > 0`而不是`where stock = getStock`

### 一人一单

单机模式下
- 首先通过用户id查询该用户的秒杀券订单
- 如果订单数量大于0，则禁止该用户下单
- 如果订单数量等于0，则扣减库存，允许用户购买
- 将方法使用`synchronized(userId.toString().intern()) {}`加锁
  - 不能在方法内加锁的原因是：方法未执行完，锁就会被释放，而此时事务没有提交，同用户的其他线程便可以获取锁，产生其他订单
  - 使用`intern()`的原因是：如果只使用`toString()`，那么底层会new一个新的对象，无法保证同一个用户id的锁是同一个，而`intern()`会从字符串常量池中获取已存在的相同字符串
- 通过`AopContext.currentProxy()`获取当前serviceImpl的代理对象，通过proxy调用创建订单方法，保证该方法按事务执行，否则事务会失效

但是集群下锁会失效，因为Java中锁是在一个JVM内部实现的，而集群下会存在多台机器，多个JVM。这种问题可以使用分布式锁来实现。

### 分布式锁

分布式锁：满足分布式系统或集群模式下多进程可见并且互斥的锁

分布式锁要满足：
- 高性能
- 高可用
- 多进程可见
- 互斥
- 安全性

基于Redis的分布式锁

实现分布式锁时要实现的两个方法：
- 获取锁
  - 互斥：确保只有一个线程可以获得锁，利用Redis的`SETNX key value`命令
- 释放锁
  - 手动释放：利用`DEL key`命令
  - 超时释放：添加锁时，设置超时时间，利用`EXPIRE key time`命令

#### 问题1：误删除锁

上面的实现可能会导致 误删除锁 问题
- 假设线程1获取锁之后阻塞，超出超时时间，锁被删除
- 线程2此时可以获取锁，在线程2执行过程中，线程1开始执行并结束，此时会删除线程2的锁，导致唯一的分布式锁被删除
- 此时其他线程可以获取分布式锁，导致多个线程同时执行

解决方案：
- 在获取锁的时候存入线程标识（可以使用UUID）
- 在释放锁时判断线程标识是否为自己，一致则释放锁，否则不释放锁

#### 问题2：删除锁操作的原子性

- 假设线程1已经执行完业务，判断锁标识与自己一致，可以删除锁，但是在删除之前进入阻塞，超时，导致锁被超时释放
- 此时线程2可以获取锁，在线程2执行过程中，线程1结束阻塞，删除了锁，导致唯一的分布式锁被删除
- 此时其他线程可以获取分布式锁，导致多个线程同时执行

#### Redis的Lua脚本 解决问题2

Redis中，可以调用Lua脚本，来执行命令
- `EVAL script numkeys key [key...] arg [arg...]`
- `EVAL "redis.call('set', 'name', 'Javk')" 0`
- `EVAL "redis.call('set', KEYS[1], ARGV[1])" 1 name rose`  注意数组下标从1开始

使用RedisTemplate中的execute(script, KEYS, ARGV)方法

### 分布式锁的优化

#### 基于SETNX实现的分布式锁存在下列问题：
- 不可重入：同一个线程无法多次获取同一把锁
- 不可重试：获取锁只尝试一次就返回错误，没有重试机制
- 超时释放：锁超时释放虽然可以避免死锁，但当业务执行时间较长时，也会导致锁释放，存在安全隐患
- 主从一致：如果Redis提供了主从集群，主从同步存在延迟，当主节点宕机时，如果从节点还没有同步主节点的锁，那么其他线程可能会获取到锁

#### Redisson
Redisson是一个在Redis的基础上实现的java驻内存数据网格 (In-Memory Data Grid)。它不仅提供了一系列的分布式的Java常用对象，还提供了许多分布式服务，其中就包含了各种分布式锁的实现。

Redisson可重入锁原理：
- 使用Redis的hash类型，key是锁的key，只能由一个线程获取，value中的field是线程唯一标识，用于线程检查当前获取锁的线程是否为自己，value中的value是计数器，表示重入次数，当计数器为0时，表示业务执行完毕，释放锁
- 底层通过调用lua脚本实现
- 流程：
- 获取锁
  - 判断当前锁是否存在
    - 不存在，获取锁并设置有效期
    - 锁已经存在，判断threadId是否为自己
      - 是自己，重入次数+1，设置有效期
- 释放锁
  - 判断当前锁是否被自己持有
    - 如果不是自己，直接返回
    - 是自己的锁，重入次数-1
      - 重入次数>0，返回
      - 重入次数=0，删除锁

## Redis分布式缓存

单点Redis存在的问题：
- 数据丢失
  - 数据持久化
- 并发能力
  - Redis主从集群，实现读写分离
- 故障恢复
  - Redis哨兵，实现健康检测和自动恢复
- 存储能力
  - 搭建分片集群，利用插槽机制实现动态扩容

### Redis 持久化

1. RDB, Redis Database Backup file

RDB也叫数据快照，将内存中的所有数据备份到磁盘。当Redis实例故障重启后，从磁盘读取快照文件，恢复数据。

```
save # 由Redis主进程执行RDB，会阻塞所有命令
bgsave # 新开一个子进程执行，避免主进程受影响
save 300 1 # 在300s内至少有1个key被修改，则执行bgsave，可以在redis.conf配置中找到
```

RDB的实现：bgsave开始的时候会fork一个子进程，子进程共享主进程数据。完成fork后，读取内存数据，写入RDB文件。父进程的页表被复制到子进程中。

fork采用的是copy-on-write技术：
- 当主进程执行读操作时，访问共享内存
- 当主进程执行写操作时，会拷贝要操作的数据为副本，对副本进行写操作，后续对该数据的读取也通过副本

RDB缺点
- 执行间隔时间长，两次之间故障可能导致数据丢失
- fork子进程、压缩、写入RDB文件都比较耗时

2. AOF, Append Only File

Redis处理的每一个写命令，都会被记录到AOF中，可以看作命令日志文件。

AOF记录频率可通过`appendfsync`来指定
- always：每次写命令就记录一次
- everysec：每秒记录一次
- no：由操作系统决定何时将缓冲区内容写到磁盘

AOF会记录每一条命令，且会记录对同一个key的多次写操作，所以文件比RDB大很多。可以通过`bgrewriteaof`命令，重写AOF文件。

||RDB|AOF|
|--|--|--|
|持久化方式|定时对内存做快照|记录每一次执行的命令|
|数据完整性|低，间隔时间长可能会丢失|高，取决于刷盘策略|
|文件大小|小，只记录值|大，记录每条命令|
|宕机恢复速度|很快|慢|
|数据恢复优先级|低，数据完整性不如AOF|高|
|系统资源占用|占用CPU和内存多|低，主要是磁盘IO，但重写时占用大量CPU和内存|
|使用场景|对数据安全要求较低|数据安全性要求高|

### Redis 主从

搭建Redis主从
1. 启动多个Redis客户端，指定不同的端口
2. 在从节点客户端中，执行`slaveof host port`命令（暂时性，关闭客户端后会失效），设置为主节点的从节点
3. 在主节点中写操作可以同步到从节点，从节点中不能写，只能读

数据同步原理

主从第一次同步是全量同步，过程如下：
- 第一阶段：master请求数据同步，通过replid判断slave是否是第一次连接，若是第一次，返回master的replid和offset，slave保存版本信息
- 第二阶段：master执行bgsave，生成RDB文件，同时将备份期间执行的命令到repl_baklog文件中；发送RDB文件，slave清空本地文件，加载RDB
- 第三阶段：master不断发送repl_baklog，slave执行接收到的命令，进行同步

master如何判断slave是不是第一次连接：
- replication id：简称replid，是数据集的标记，id一致则说明是同一个数据集。每一个master都有一个唯一的replid，从节点会继承这个id
- offset：偏移量。随着记录在repl_baklog中的数据的增多而逐渐增长，slave完成同步时也会记录offset。若slave的offset小于master，则说明需要进行数据同步

主从第一次同步流程：
- slave节点请求增量同步
- master节点判断replid，发现不一致，拒绝增量同步
- master将完整数据生成RDB，发送到slave
- slave清空本地数据，加载RDB
- master将RDB期间的命令记录在rep_baklog，并持续将log中的命令发送到slave
- slave接收到同步的命令，执行命令

slave重启后同步，执行增量同步：
- slave请求增量同步
- master判断replid一致，回复continue
- master在repl_baklog中获取offset后的数据
- 发送到slave，进行同步

repl_baklog是一个环形数组，记录满后覆盖最早的记录。若slave断开时间太久，尚未备份的数据也被覆盖，则无法基于log做增量同步，只能再做全量同步。

主从同步优化：
- 设置`repl-disless-sync`为yes，启用无磁盘同步，避免全量同步时的磁盘IO，适用于网络IO速度快的场景
- Redis单节点内存占用不要太大，避免RDB过大导致磁盘IO过大
- 适当提高repl_baklog大小，尽量避免覆盖同步前数据，发现slave宕机时尽快恢复，尽量避免全量同步
- 限制一个master上的slave节点数量，可以采用'主-从-从'链式结构，减轻master压力

### Redis 哨兵

哨兵机制用来实现主从集群的自动故障恢复
- 监控：Sentinel不断监控master和slave是否正常工作
- 自动故障恢复：若master故障，Sentinel会将一个slave提升为master。当故障实例恢复后，也以新的master为主
- 通知：Sentinel充当Redis客户端的服务发现来源，当集群故障，发生主从转换时，Sentinel将最新信息推送给客户端

哨兵服务状态监控：
- 基于心跳检测机制，每隔1s向集群中的每个实例发送ping请求
  - 主观下线：若未在规定时间内收到某个实例的响应，则认为他主观下线
  - 客观下线：若超过指定数量（quorum）的哨兵认为某个实例主管下线，则该实例客观下线。quorum一般设置为哨兵数量的一半

新的master选举：一旦master故障，要从slave中选取一个成为新的master
- 首先判断slave节点与master节点断开时间长短，若长于指定值（down-after-milliseconds * 10）则会排除该节点
- 然后判断slave节点的slave-priority值，越小优先级越高，为0则永不参与选举
- 再判断slave节点的offset，offset越大则数据越新，优先级越高
- 最后判断slave节点的id值，id越小优先级越高

故障转移：当选中了其中一个slave节点作为master后
- Sentinel给该slave节点发送`slaveof no one`命令，使其成为master
- Sentinel给其余slave节点发送`slaveof newhost newport`命令，使它们称为新master的slave，开始从新的master同步数据
- 最后Sentinel将故障master标记为slave，当故障恢复后，自动成为新master的slave

搭建哨兵集群
```cmd
port 27001
sentinel announce-ip 127.0.0.1
sentinel monitor mymaster 127.0.0.1 7001 2 # 2是quorum，判断客观下线的阈值
sentinel down-after-milliseconds mymaster 5000
sentinel failover-timeout mymaster 60000
dir "./redis_practice/s1"
```

RedisTemplate使用哨兵
```java
// 引入redis starter依赖
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
// 配置节点地址
spring:
  redis:
    sentinel:
      master: mymaster
      nodes:
        - 127.0.0.1:27001
        - 127.0.0.1:27002
        - 127.0.0.1:27003

// 配置lettuce主从读写分离
@Bean
public LettuceClientConfigurationBuilderCustomizer configurationBuilderCustomizer() {
  return configBuilder -> configBuilder.readFrom(ReadFrom.REPLICA_PREFERRED);
}
```

这里的ReadFrom是配置Redis的读取策略，是一个枚举类型，包括：
- MASTER
- MASTER_PREFERRED
- REPLICA
- REPLICA_PREFERRED

### Redis 分片集群

分片集群的特征：
- 集群中有多个master，每个master可以保存不同的信息
- 每个master都可以有多个slave节点
- master之间通过ping检测健康状态
- 客户端请求可以访问任意节点，最终都会被转发到正确节点

创建集群
```cmd
redis-cli --cluster --cluster-replicas 1 127.0.0.1:7001 127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:8001 127.0.0.1:8002 127.0.0.1:8003
# 其中1指定每个master有1个slave，此时节点总数/(replicas + 1)就是master节点数量，因此节点列表中前3个是master，后面的slave随机分配给master
```
#### 散列插槽
散列插槽：Redis会把每一个master节点映射到0~16383共16384个插槽中（hash slot）

数据key不是跟节点绑定，而是跟插槽绑定。Redis会根据key的有效值，计算插槽值，方法如下：
- 如果key中没有{}，那么整个key都是有效值
- 如果key中有{}，且{}中至少有一个字符，那么{}内部的是有效值
- 将有效值根据CRC16算法，得到一个hash值，然后对16384取余，得到slot值

#### 集群伸缩

```cmd
# 添加节点的命令，默认主节点，若指定slave，也可指定属于哪个master
add-node newHost:newPort existingHost:existingPort --cluster-slave -- cluster-master-id <arg>
```

#### 故障转移

- 首先该实例与其他实例失去连接
- 然后疑似宕机
- 最后确定下线，提升一个slave为新的master

利用cluster failover命令可以手动让集群中的某个master宕机，切换到执行cluster failover命令的这个slave节点，实现无感知的数据迁移。其流程如下:
1. slave节点告诉master节点拒绝任何客户端请求
2. master返回当前数据的offset给slave
3. slave等待数据offset与master一致
4. slave和master开始故障转移
5. slave标记自己为master，广播故障转移结果
6. 原master收到广播，开始处理客户端读请求


手动的Failover支持三种不同模式
- 缺省:默认的流程，如图1~6步
- force:省略了对offset的一致性校验
- takeover:直接执行第5步，忽略数据一致性、忽略master状态和其它master的意见

RedisTemplate访问分片集群
```java
// 1. 引入redis的starter依赖
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

// 2. 配置分片集群地址

spring:
  redis:
    cluster:
      nodes:
        - 127.0.0.1:7001
        - 127.0.0.1:7002
        - 127.0.0.1:7003
        - 127.0.0.1:8001
        - 127.0.0.1:8002
        - 127.0.0.1:8003
// 3. 配置读写分离

```

## 多级缓存

浏览器客户端缓存 -> NGINX本地缓存 -> Redis缓存 -> Tomcat进程缓存

## Redis 最佳实践

### BigKey

BigKey，通常以key本身的大小，和key中成员数量来综合判定
- key本身数据量过大，例如一个key 5MB
- key集合中包含的元素过多，例如一个set中有10000个成员
- key中包含的数据量过大，例如一个hash中只有1000个成员，但是value总大小达到100MB

推荐值：
- 单个key小于5KB
- 对于集合类型的key，成员数量小于1000个

BigKey的危害：
- 网络阻塞：对BigKey进行读请求时，少量QPS，就有可能阻塞网络带宽，导致Redis实例甚至其所在的物理机变慢
- 数据倾斜：BigKey所在的Reds实例的内存使用率远超其他实例，无法使数据分片的内存资源达到均衡
- Redis阻塞：对元素较多的hash、set、zset进行集合运算时耗时较久，阻塞主线程
- CPU压力：对BigKey进行序列化和反序列化会导致CPU使用率飙升

删除BigKey：
- Redis 3.0及以下版本，如果是集合类型，会遍历集合的元素，进行依次删除
- Redis 4.0及以后，使用`unlink`进行异步删除

### 恰当的数据类型

hash类型的key，其中有100w对field value，field是自增id，这个key有什么问题：
1. hash的entry数量超过500时，会使用hash表而不是ziplist存储，内存占用多
2. 可以通过hash-max-ziplist-entries设置entry上限，但是会导致BigKey问题
3. 将大的hash拆分为许多小的hash，以`id / 100`作为key，`id % 100`作为field，每100个元素作为一个hash

### 批处理

#### pipeline

MSET可以处理较多数据，但是操作的数据类型有限，因此使用pipeline。

但是pipeline稍慢于MSET，因为MSET是Redis内置命令，具有原子性；而pipeline中的多个命令之间没有原子性

#### 集群下的批处理

若Redis是一个集群，使用MSET和pipeline时，必须使所有key的hash值落在同一个节点的插槽中，否则就会执行失败。

解决方案：给所有key设计相同的hash_tag，则它们的slot都相同（不推荐）

推荐：并行slot

### 服务端优化

#### 持久化配置

用来缓存的Redis实例，尽量不要开启持久化

建议关闭RDB持久化功能，开启AOF

利用脚本定期在slave节点做RDB，实现数据备份

设置合理的bgrewrite阈值，避免频繁的bgrewrite

配置`no-appendfsync-on-rewrite`为yes，禁止在AOF期间做rewrite，避免引起阻塞

#### 慢查询

Redis中可以记录慢查询日志

`slowlog-log-slower-than`，指定了慢于多少微秒的查询会被记录，默认为10000

`slowlog-max-len`，指定了最多有多少条慢查询日志被记录，默认为128

#### 命令及安全配置

Redis暴露到公网上，没有设置密码，会被人利用ssh漏洞攻击。攻击者将自己的公钥文件set到Redis中，然后通过`config set`动态设置`dir`为`/root/.ssh/`，然后将dbfilename设置为`authorized_keys`。这样，当Redis持久化文件时，攻击者的公钥会被写入root目录下，可通过ssh远程登录了

#### 内存配置

内存占用：
- 数据内存：Redis内存最主要的部分，是存储Redis的键值信息。主要问题是BigKey、内存碎片。
- 进程内存：Redis主进程本身占用的内存，很小。
- 缓冲区内存：客户端缓冲区、AOF缓冲区、复制缓冲区。客户端缓冲区又分为输入缓冲区、输出缓冲区。这部分内存占用波动较大，不当使用BigKey，可能内存溢出。
  - 复制缓冲区：主从复制的repl_backlog_buf，如果太小，可能导致频繁全量同步，影响性能。可以通过`repl-backlog-size`来设置，默认1MB。
  - AOF缓冲区：AOF的刷盘缓冲区，AOF的rewrite缓冲区
  - 客户端缓冲区：输入缓冲区最大不超过1GB且不能设置。输出缓冲区可以设置。

#### 集群配置

集群完整性问题：在Redis的默认配置中，若有一个插槽不可用，会导致整个集群停止对外服务。为了保证高可用性，最好将`cluster-require-full-coverage`设置为no。

集群带宽问题：Redis集群节点会不断向其他节点发送PING，来确定其他节点的状态。PING的信息包括：插槽信息、集群状态信息。集群节点越多，信息大小越大，10个节点的信息大小可达1KB。

解决方法：
- 避免大集群，控制在1000以内。再多可设置多个集群。
- 避免在单个物理机中运行过多Redis实例。
- 合理设置`cluster-node-timeout`的值

## Redis 数据结构

### 动态字符串 SDS

Redis没有直接使用C语言的字符串，因为：
- 获取字符串长度需要运算
- 非二进制安全，若读取到`\0`，则可能误判字符串结尾
- 不可修改

Redis构建了自己的字符串数据结构：Simple Dynamic String

sdshdr8的源码如下：
```C
struct __attribute__ ((__packed__)) sdshdr8 {
    uint8_t len; /* buf已保存的字符串字节数，不包含结束标示*/
    uint8_t alloc; /* buf申请的总的字节数，不包含结束标示*/
    unsigned char flags; /* 不同SDS的头类型，用来控制SDS的头大小*/
    char buf[];
}
```

SDS动态扩容 （内存预分配）：
- 若新字符串（原字符串 + 追加的字符串）小于1M，则新空间是扩展后的字符串长度 * 2 + 1
- 若新字符串大于1M，则新空间为扩展后字符串长度 + 1M + 1

SDS的优点：
1. O(1)时间复杂度获取字符串长度
2. 减少内存分配次数，提高性能
3. 二进制安全，解决\0问题
4. 支持动态扩容

### IntSet

IntSet是Redis中Set集合的一种实现方式，基于C的整数数组实现，具备长度可变、有序等特征。

```C
typedef struct intset {
    uint32_t encoding; /* 编码方式，支持存放16位、32位、64位整数*/
    uint32_t length; /* 元素个数 */
    int8_t contents[]; /* 整数数组，保存集合数据*/
} intset;
```

IntSet会将整数升序排序在contents数组中，访问元素根据`startPtr + (sizeof(int16_t) * index)`来实现，因此要求存储的数据所占字节数必须一致。

若存入了超出当前数据类型范围的数据：
1. IntSet会自动升级编码方式到合适的大小
2. 并按照新的编码方式及元素个数进行扩容
3. **倒序**将原数组中的元素拷贝到扩容后的正确位置
4. 最后将待添加的元素放入数组末尾
5. 修改encoding和length属性

IntSet的特点总结：
1. Redis会确保IntSet中的元素唯一、有序
2. 具备类型升级机制，可以节省内存空间
3. 底层采用二分查找，进行扩容
4. 数据量大时，申请连续的内存空间较为困难，仅适合数据量较小的场景

### 字典 Dict

```C
typedef struct dictht {
    // entry数组
    //数组中保存的是指向entry的指针
    dictEntry **table;
    // 哈希表大小，size的值是2^n
    unsigned long size;
    // 哈希表大小的掩码，总等于size - 1
    unsigned long sizemask;
    // entry个数unsigned long used;
    unsigned long used;
} dictht;

typedef struct dictEntry {
    void *key;// 键
    union {
        void *val;
        uint64_t u64;
        int64_t s64;
        double d;
    } v; // 值
    // 下一个Entry的指针
    struct dictEntry *next;
} dictEntry;

typedef struct dict {
    dictType *type; // dict类型，内置不同的hash函数
    void *privdata; // 私有数据，在做特殊hash运算时用
    dictht ht[2]; // 一个Dict包含两个哈希表，其中一个是当前数据，另一个一般是空，rehash时使用
    long rehashidx; // rehash的进度，-1表示未进行
    int16_t pauserehash; // rehash是否暂停，1则暂停，o则继续
} dict;
```

Dict中的HashTable是数组结合单向链表实现。当集合中元素过多时，导致哈希冲突、链表过长，查询效率大大降低。Dict每次新增时都会检查负载因子`LoadFactor = used / size`，满足以下两种情况时会发生哈希扩容：
- 哈希表的`LoadFactor >= 1`，并且服务器没有执行bgrewriteaof或bgsave
- 哈希表的`LoadFactor > 5`，强制扩容

Dict每次删除时，也会检查LoadFactor是否小于0.1，是则进行哈希表收缩

哈希表扩容或收缩后，sizemask和size都会变化，因此必须rehash，过程如下：
1. 计算新哈希表的realSize
    - 如果是扩容，realSize是第一个大于等于`dict.ht[0].used + 1`的2^n
    - 如果是收缩，realSize是第一个大于等于`dict.ht[0].used`的2^n
2. 按新的realSize申请内存空间，创建dicht，赋值给dict.ht[1]
3. 设置dict.rehashidx=0，标志开始rehash
3. (渐进式hash)，每次执行增删改查操作时，判断rehashidx是否大于-1，如果是则将dict.ht[0].table[rehashidx]的entry链表rehash到dict.ht[1]，并使rehashidx++，知道dict.ht[0]的所有数据都转移到dict.ht[1]中
4. 将dict.ht[0]里面的每一个dictEntry，rehash到新的dict.ht[1]中
5. 将dict.ht[1]赋值给dict.ht[0]，将dict.ht[1]初始化为空哈希表，释放dict.ht[0]的内存
6. 将rehashidx设置为-1
7. 期间新增操作，都在dict.ht[1]上进行；删除、修改、查询操作，在0和1同时进行，保证ht[0]的数据只减不增

为了避免主线程阻塞，redis进行的是渐进式rehash

### ZipList

ZipList是一种特殊的双端链表，由一系列特殊编码的连续内存块组成。可在任意一端进行压入/弹出操作，时间复杂度为O(1)

ZipList中包含以下属性：
- zlbytes：记录整个ZipList所占内存字节数
- zltail：记录起始地址到最后一个entry的起始地址有多少字节
- zllen：记录节点数量
- entry：ZipList中的数据单元
- zlend：标志ZipList的结尾

ZipList中的entry大小并不记录前后节点的指针，因为两个指针占16字节，而是采用了下面的ZipListEntry结构：
- previous_entry_length：前一个节点的长度，1字节或5字节
  - 若前一节点的长度小于254字节，则使用1字节
  - 若前一节点的长度大于254字节，则使用5字节，第一个字节为0xFE，后四个字节是真实的数据
- encoding：编码属性，记录content的数据类型（字符串/整数）及长度，占用1、2或5字节
  - 字符串：如果前两位是00、01、10开头，则content是字符串
  - 整数：前两位是11开头，且encoding固定占用1个字节
- contents：保存节点数据，字符串或整数

ZipList的**连锁更新**问题：假设有N个连续的、长度为250-253字节的entry，因此entry的previous_entry_length用1个字节表示。此时在ZipList头插入254字节的数据，第二个节点的previous_entry_length大小变为5字节，导致该节点总大小超过254字节，连锁导致后面节点进行扩展，形成连锁更新。新增、删除都可能导致连锁更新。

### QuickList

QuickList是一个双端链表，链表中的每一个节点都是一个ZipList。可以解决ZipList占用连续内存空间过长的问题。

Redis中可以设置`list-max-ziplist-size`
- 如果是整数，则表示QuickList中的每个ZipList中的entry的最大个数
- 如果是负数，则表示限制ZipList的最大内存大小，分为五种情况
  - -1：每个ZipList内存占用不超过4KB
  - -2：每个ZipList内存占用不超过8KB
  - -3：每个ZipList内存占用不超过16KB
  - -4：每个ZipList内存占用不超过32KB
  - -5：每个ZipList内存占用不超过64KB

QuickList还可以对ZipList进行压缩，设置`list-compress-depth`，链表首尾不压缩的节点个数，中间节点都压缩
- 0：不压缩
- 1：压缩首尾1个节点
- 2：压缩首尾2个节点
- ...

### 跳表 SkipList

SkipList是一个特殊的链表，与传统链表有一些差异：
- 元素升序存储
- 节点中可能包含多个指针，指针跨度不同

```C
// t_zset.c
typedef struct zskiplist {
    // 头尾节点指针
    struct zskiplistNode *header， *tail;
    // 节点数量
    unsigned long length;
    // 最大的索引层级，默认是1
    int level;
} zskiplist;

// t_zset.c
typedef struct zskiplistNode {
    sds ele; // 节点存储的值
    double score;// 节点分数，排序、查找用
    struct zskiplistNode *backward; // 前一个节点指针
    struct zskiplistLevel {
        struct zskiplistNode *forward; // 下一个节点指针
        unsigned long span; // 索引跨度
    } level[]; // 多级索引数组
} zskiplistNode;
```

每个节点都包含ele和score，ele存储数据，score用于节点排序。节点按score排序，若score相同，则按ele字典序排序。

SkipList中含有level多级索引数组，用于存储每个节点的forward指针。level数组中至少有一个元素，指向下一个节点，有多级索引的节点就在level中设置。最大层数为32。

增删改查效率与红黑树基本一致，实现更简单。

### RedisObject

Redis中任意数据类型的键值都会被封装成RedisObject，也叫Redis对象

![Alt text](pictures/image.png)

|数据类型|编码方式|
|-|-|
|OBJ_STRING|int、embstr、raw|
|OBJ_LIST|LinkedList + ZipList (v3.2以前)，QuickList (v3.2以后)|
|OBJ_SET|intset、HT|
|OBJ_ZSET|SkipList、ZipList、HT|
|OBJ_HASH|ZipList、HT|

## Redis 数据类型

### String

基本编码方式为raw，基于SDS实现，上限512MB

如果存储的SDS长度小于44字节，会采用embstr编码，将Object head与SDS存储在同一块连续空间，只需要申请一次内存，提高效率。44字节数据 + 1字节结束符 + 3字节头信息 + RedisObject 16字节 = 64字节，刚好是内存分片大小，不会产生内存碎片。

如果存储的是整数，并且大小在LONG_MAX范围内，会采用INT编码，直接将数据保存在RedisObject的ptr位置(8字节)，不需要SDS

### List

在Redis 3.2之前，ZipList + LinkedList实现，当元素数量小于512且元素大小小于64字节时，使用ZipList，超过则采用LinkedList；在Redis 3.2 之后，使用QuickList实现

### Set

Set是Redis中的单列集合，特点：
- 无序
- 保证元素唯一
- 求交集、并集、差集

为了保证查询效率和唯一性，采用Dict编码实现Set。Dict中的key存储数据，value为null

当存储的的所有元素都是整数时，且个数不超过`set-max-intset-entries`，Set会采用IntSet编码，节省内存

### ZSET

ZSET，也就是SortedSet，其中每个元素都要指定score值和member值
- 可以根据score值排序
- 确保member值的唯一性
- 根据member查询score

实现一：ZSET由dict和SkipList实现，dict处理键值对，SkipList实现排序，缺点是内存占用太高。

实现二：当元素数量不多时，HT和SkipList优势不明显，此时采用ZipList实现ZSET，节省内存，需要满足以下条件：
- 元素数量zset-max-ziplist-entries，默认128；如果设置为0，则禁用ZipList
- 元素大小不超过zset-max-ziplist-value字节，默认64字节

ZipList实现排序+键值：
- ZipList本身是连续内存，通过将score和member存储在相邻的entry，实现键值对
- 将score小的排在前面，大的排在后面，实现按score升序排序

### Hash

Hash底层编码与Zset基本一致，去掉了排序相关的SkipList
- 默认采用ZipList，相邻的2个entry存储field和value
- 当数据量较大时，ZipList转为Dict，满足下面两个条件
  - ZipList中元素数量超过hash-max-ziplist-size，默认512，或
  - ZipList中任意entry大小超过hash-max-ziplist-value，默认64字节

## Redis 网络模型

### 用户空间和内核空间

为避免程序导致系统崩溃，要将用户应用与内核分离

进程的寻址空间会划分为两部分：内核空间、用户空间

### 阻塞IO

阻塞IO就是两个阶段必须阻塞等待：
1. 等待数据就绪
2. 从内核拷贝数据到用户空间

### 非阻塞IO

非阻塞IO的recvfrom操作会立即返回结果而不是阻塞用户进程。

在非阻塞IO的两个阶段中，用户进程在第一个阶段是非阻塞的，在第二个阶段是阻塞的。虽然非阻塞，但是性能没有显著提高。反而忙等机制会导致CPU空转，CPU使用率暴增。

### IO多路复用

无论是阻塞IO还是非阻塞IO，用户应用在第一阶段都会使用recvfrom来获取数据，差别在于无数据时的处理方案：
- 若recvfrom恰好无数据，阻塞IO会使进程阻塞，非阻塞IO使CPU空转

服务器处理客户端socket请求时，在单线程情况下，只能依次处理每个socket。若socket恰好在socket未就绪，线程就会阻塞、所有其他客户端的socket必须等待。

文件描述符(File Descriptor, FD)，是从0开始递增的一个unsigned整数，用来关联Linux中的文件。Linux中一切皆文件，包括socket

IO多路复用，就是通过单线程监听多个FD，当FD可读、可写时得到通知，从而避免无效等待，充分利用CPU资源
- 进程调用select，传入多个socket的FD，并阻塞等待数据，任意FD就绪，就返回readable
- 进程反复调用recvfrom并等待返回成功标识

监听FD在Linux中有三种实现：
- select，poll，epoll
- 其中select和poll只会通知用户有FD就绪，但无法知道具体哪一个FD就绪
- epoll不仅会通知有FD就绪，还会将已就绪的FD写入用户空间

#### select模式

流程：
1. 用户空间中创建fd_set
2. 要监听的FD是1/2/5
3. 执行`select(5 + 1, null, null, 3)`
4. 将fd_set拷贝到内核空间
5. 内核空间遍历fd_set
6. 若没有就绪的，就休眠
7. 等待数据就绪被唤醒或超时，修改fd_set的值
8. 将fd_set拷贝回用户空间
9. 进程读取fd_set，遍历获得就绪的FD

缺点：
1. 需要将整个fd_set从用户空间拷贝到内核空间，select结束后还需要拷贝回去
2. select无法知道具体哪个FD就绪
3. fd_set监听的FD数量不能超过1024

#### poll模式

源码
```C
// pollfd 中的事件类型
#define POLLIN //可读事件
#define POLLOUT //可写事件
#define POLLERR //错误事件
#define POLLNVAL //fd未打开

// pollfd结构
struct pollfd {
    int fd; /*要监听的fd */
    short int events; /* 要监听的事件类型: 读、写、异常 */
    short int revents; /*实际发生的事件类型 */
}; 

// poll函数
int poll (
    struct pollfd *fds，// pollfd数组，可以自定义大小
    nfds_t nfds，// 数组元素个数
    int timeout // 超时时间
);
```

流程：
1. 创建pollfd数组，向其中添加关注的fd信息，数组大小自定义
2. 调用poll函数，将pollfd数组传到内核空间，数组大小无上限
3. 内核遍历fd，是否就绪
4. 数据就绪或超时后，返回pollfd数组到用户空间，同时返回就绪FD个数n
5. 用户进程判断n是否大于0
6. 大于0则遍历pollfd数组，找到就绪的FD

与select对比：
1. 增加了pollfd数组，可以自定义无上限的监听的fd数量
2. 监听fd越多，遍历越久，性能会下降

#### epoll

源码
```C
struct eventpoll {//...
    struct rb_root rbr; // 一颗红黑树，记录要监听的
    FDstruct list_head rdlist;// 一个链表，记录就绪的FD//...
};
// 1.会在内核创建eventpoll结构体，返回对应的句柄
epfdint epoll_create(int size);

// 2.将一个FD添加到epoll的红黑树中，并设置ep_poll_callback
// callback触发时，就把对应的FD加入到rdlist这个就绪列表中
int epoll_ctl(
    int epfd, // epoll实例的句柄
    int op, // 要执行的操作，包括: ADD、MOD、DEL
    int fd, // 要监听的事件类型: 读、写、异常等
    struct epoll_event *event
);

// 3.检查rdlist列表是否为空，不为空则返回就绪的FD的数量
int epoll_wait(
    int epfd, // eventpoll实例的句柄
    struct epoll_event *events, //空event数组，用于接收就绪的
    FDint maxevents, int timeout // 超时时间，-1用不超时; 不阻塞;大于o为阻塞时间
);
```

流程：
1. `epoll_create(1)`创建epoll实例
2. `epoll_ctl(...)`添加要监听的fd，关联callback
3. `epoll_wait()`等待fd就绪

优点：
1. 减少了fd的拷贝次数，因为通过`epoll_ctl`添加要监听的fd之后，fd会在内核空间被加到红黑树上，循环wait的过程中无需重复拷贝
2. 减少了fd的拷贝数量，仅需要拷贝就绪的fd
3. 用户程序不需要遍历所有fd，因为只将就绪的fd传到了用户空间
4. 监听的fd的数量可以很多，不会导致性能下降，因为采用了红黑树存储要监听的fd，而不是链表

#### 事件通知机制

当FD有数据可读时，我们调用epoll_wait就可以得到通知。事件通知的模式有两种：
- LevelTriggered：LT。当FD有数据可读时，会重复通知，直到数据处理完成，是epoll默认模式
- EdgeTriggered：ET。当FD有数据可读时，只通知一次，不管数据是否处理完成。

LT存在的问题：
1. 性能较低，因为会重复通知
2. 多个进程监听同一个fd，都在调用epoll_wait。当fd就绪时，所有进程都会被唤醒，但只需要一个进程获取数据

### 信号驱动IO

1. 用户应用发起sigaction系统调用，内核空间不返回数据，在用户空间建立SIGIO信号处理函数 （非阻塞）
2. 若有FD就绪，内核发送SIGIO信号到用户应用
3. 进程调用recvfrom

### 异步IO

1. 用户应用调用aio_read系统调用，内核返回ok，无阻塞
2. 内核中数据就绪，拷贝完成后，递交aio_read中指定的信号，用户应用中再由信号处理函数处理信号

缺点：高并发场景下，用户端发送过多aio_read，内核压力过大

### Redis网络模型

Redis是单线程吗？
- Redis的核心业务部分：命令处理部分，单线程
- 整个Redis，多线程
- 在Redis 4.0，引入多线程异步处理耗时较久的任务，异步删除命令unlink
- 在Redis 6.0，在核心网络模型引入多线程，进一步提高对多核CPU的利用率

为什么Redis坚持使用单线程？
- Redis是纯内存操作，它的性能瓶颈是网络延迟而不是执行速度，使用多线程并不会显著提升性能
- 多线程导致过多的上下文切换，带来不必要的开销
- 引入多线程会带来线程安全问题，必然引入线程锁等安全手段，实现复杂度高，性能也会大打折扣

Redis通过IO多路复用提升网络性能，支持多种不同形式的多路复用实现，并进行了封装，提供了统一的高性能事件库API库 AE

![Alt text](pictures/Redis网络模型.png)

根据网络模型，发现Redis性能瓶颈在解析客户端命令、写响应结果，因此在这两部分采用了多线程，核心的命令执行、IO多路复用依然是由主线程执行。

## Redis 通信协议

### RESP协议 Redis Serialization Protocol

RESP中，通过首字节的不同字符来区分不同数据类型，共有5种：
- 单行字符串，首字节是+，后面跟上单行字符串，最后以\r\n结尾。例如返回OK，"+OK\r\n"
- 错误Errors，首字节是-，与单行字符串格式相同，内容替换为错误信息。例如，"-Error Message\r\n"
- 数值，首字节是:，后面跟上数字格式的字符串，以\r\n结尾。例如，":50\r\n"
- 多行字符串，首字节是$，表示二进制安全的字符串，最大支持512MB。例如，"$5\r\nhello\r\n"
  - 如果大小为0，表示空串
  - 如果大小为-1，表示不存在
- 数组，首字节是*，后面跟上数组元素个数，再跟上元素，元素数据类型不限。例如，"*3\r\n$3\r\nset\r\n$4\r\nname\r\n$7\r\nlixueqi\r\n"，`set name lixueqi`

## Redis 内存回收

### 过期策略

Redis中所有的key value保存在Dict数据结构中，不过在database结构体中，有两个Dict：一个用来记录key value，另一个用来记录key TTL

TTL到期是否立即删除key
- 惰性删除：不是在TTL到期时立即删除，在访问一个key的时候，检查该key的存活时间，如果已经过期才删除
  - 存在问题：若过期的key，再也没有人访问，则永远都不会被删除
- 周期删除：通过一个定时任务，周期性地抽样部分过期的key，然后执行删除
  - SLOW模式：设置定时任务serverCron()，按照server.hz的频率来执行删除
  - FAST模式：每个事件循环前会调用beforeSleep()函数，执行过期key清理

SLow模式规则:
1. 执行频率受server.hz影响，默认为10，即每秒执行10次，每个执行周期100ms。
2. 执行清理耗时不超过一次执行周期的25%
3. 逐个遍历db，逐个遍历db中的bucket，抽取20个key判断是否过期
4. 如果没达到时间上限 (25ms)并且过期key比例大于10%，再进行一次抽样，否则结束

FAST模式规则(过期key比例小于10%不执行)
1. 执行频率受beforesleep()调用频率影响，但两次FAST模式间隔不低于2ms
2. 执行清理耗时不超过1ms
3. 逐个遍历db，逐个遍历db中的bucket，抽取20个key判断是否过期
4. 如果没达到时间上限 (1ms)并且过期key比例大于10%，再进行一次抽样，否则结束

### 淘汰策略

内存淘汰:就是当Redis内存使用达到设置的阈值时，Redis主动挑选部分key删除以释放更多内存的流程。

Redis会在执行任何命令之前检查内存是否足够

Redis支持8种不同的淘汰策略：
- noeviction：不淘汰任何key，但是内存满时不允许写入新数据，默认
- volatile-ttl：对设置了TTL的key，比较key的剩余TTL，优先淘汰TTL小的
- allkeys-random：对全体key，随机淘汰，也就是直接从db->dict挑选
- volatile-random：对设置了TTL的key，随机淘汰，也就是直接从db->expires挑选
- allkeys-lru：全体key，基于LRU算法淘汰
- volatile-lru：设置TTL的key，基于LRU算法淘汰
- allkeys-lfu：全体key，基于LFU算法淘汰
- volatile-lfu：设置TTL的key，基于LFU算法淘汰

RedisObject中，LRU_BITS
- LRU：记录以秒为单位记录最近一次访问时间，长度24bit
- LFU：高16为以分钟为单位记录最近一次访问时间，低8位记录逻辑访问次数

LFU的访问次数之所以叫做逻辑访问次数，是因为并不是每次key被访问都计数，而是通过运算:
1. 生成0~1之间的随机数R
2. 计算 1/(旧次数*lfu _log_factor + 1)，记录为P，lfu_log_factor默认为10
3. 如果R < P，则计数器 + 1，且最大不超过255
4. 访问次数会随时间衰减，距离上一次访问时间每隔lfu_decay_time 分钟(默认1)，计数器-1

![Alt text](pictures/淘汰策略.png)