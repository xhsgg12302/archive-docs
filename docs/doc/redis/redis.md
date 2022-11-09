## NoSql数据库的四大分类

* ### 表格视图

    | 分类            | Example举例                                     | 典型应用场景                                                 | 数据模型                                    | 优点                                                         | 缺点                                           |
    | --------------- | ----------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------- | ------------------------------------------------------------ | ---------------------------------------------- |
    | 键值(key-value) | Tokyo,Cabinet/Tyrant,Redis,Voldemort,Oracle BDB | 内容缓存，主要用于处理大量数据的高访问负载，也用于一些日志系统等 | key指向value的键值对，通常用hashtable来实现 | 查找速度快                                                   | 数据无结构化，通常只被当作字符串或者二进制数据 |
    | 列存储数据库    | Cassandra，HBase,Riak                           | 分布式的文件系统                                             | 以列簇式存储，将同一列数据存放在一起        | 查找速度快，可扩展性强，更容易进行分布式扩展                 | 功能相对局限                                   |
    | 文档型数据库    | CouchDB，MongoDb                                | web应用（与Key-value类似，value是结构化的，不同的是数据库能够了解Value的内容） | key-value对应的键值对，value为结构化数据    | 数据结构要求不严格，表结构可变，不需要向关系型数据库一样预先定义表结构 | 查询性能不高，而且缺乏统一的查询语法           |
    | 图形数据库      | Neo4j,InfoGrid,Infinite Graph                   | 社交网络，推荐系统等，专注于构建关系图谱                     | 图结构                                      | 利用图层结构相关算法，比如最短路径寻址等                     | 很多时候需要对整个图做计算才能得出需要的信息   |


## 分布式数据库中的CAP原理CAP+BASE

* ### 传统的acid分别是什么

    + A（Atomicity）原子性
    > 原子性很容易理解，也就是说事务里的所有操作要么全部完成，要么不做，事务成功的条件是事务里的所有操作都成功，只要有一个操作失败，整个事务就是失败的，需要回滚。比如银行转账，从A向B转100元，分为两个步骤，1）从A账户减100，2）在B账户加100。

    + C（Consistency）一致性
    > 数据库要一直处于一致的状态，事务的运行不会改变数据库原本的一致性约束
    
    + I（Isolation）隔离性
    > 所谓的隔离性是指并发的事务之间不会互相影响，如果一个事务要访问的数据正在被另一个事务修改，只要另外一个事务未提交，它所访问的数据就不受未提交事务的影响，比如有个事务要完成A——》B转账100元，只要事务还没提交（未完成）的情况下，B查询自己的账户余额，是看不到新增加的100元的
    
    + D（Durability）持久性
    
    > 持久性是指一旦事务提交后，它所做的修改将会永久的保存在数据库上，即使宕机也不会丢失。
    
* ### CAP

    + C（Consistency）强一致性
    > Consistency 中文叫做"一致性"。意思是，写操作之后的读操作，必须返回该值。举例来说，某条记录是 v0，用户向 G1 发起一个写操作，将其改为 v1。接下来，用户的读操作就会得到 v1。这就叫一致性。问题是，用户有可能向 G2 发起读操作，由于 G2 的值没有发生变化，因此返回的是 v0。G1 和 G2 读操作的结果不一致，这就不满足一致性了。为了让 G2 也能变为 v1，就要在 G1 写操作的时候，让 G1 向 G2 发送一条消息，要求 G2 也改成 v1。

    + A（Availability）可用性
    > Availability 中文叫做"可用性"，意思是只要收到用户的请求，服务器就必须给出回应。用户可以选择向 G1 或 G2 发起读操作。不管是哪台服务器，只要收到请求，就必须告诉用户，到底是 v0 还是 v1，否则就不满足可用性。
    
    + P（Partition tolerance）分区容错性
    > 先看 Partition tolerance，中文叫做"分区容错"。大多数分布式系统都分布在多个子网络。每个子网络就叫做一个区（partition）。分区容错的意思是，区间通信可能失败。比如，一台服务器放在中国，另一台服务器放在美国，这就是两个区，它们之间可能无法通信。

    > CAP 理论的核心是：一个分布式系统不可能同时很好的满足一致性，可用性和分区容错这三个需求，最多只能同时较好的满足两个。因此，根据CAP原理将NoSql数据库分成了满足CA原则，CP原则，AP原则三大类.一般来说，分区容错无法避免，因此可以认为 CAP 的 P 总是成立。CAP 定理告诉我们，剩下的 C 和 A 无法同时做到。

    + CA 单点集群，满足一致性可用性的系统，通常在可扩展性上不太强
    + CP 满足一致性，分区容错的系统，通用性能不是很高，
    + AP 满足可用性，分区容错的系统，通常可能对一致性要求低一些
    
    + BASE（辅助）
    > 为了缓解实现强一致性而降低可用性带来的问题，基本可用（Basically Available），软状态（Soft state），最终一致（Eventually consistent），思路就是让系统放松对某一时刻数据的强一致性而换取系统的可用性。
    
    
## redis(Remote Dictionary Server(远程字典服务器))

+ ### 是什么

  > 是完全开源免费的，用C语言便携，遵守BSD协议，是一个高性能的（key/value) 分布式内存数据库，基于内存运行并支持持久化的NoSql数据库，是当前最热门的NoSql数据库之一，也被人们称为数据结构服务器，与其他相比较有以下三个特点：<br>
  > 【redis 支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载并使用<br>
  > 【redis 不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储<br>【redis 支持数据的备份，即master-slave模式的数据备份

+ ### 能干嘛

  > 内存存储和持久化：redis支持异步将内存中的数据写到硬盘上，同时不影响继续服务</p>
  > 取最新N个数据的操作，如：可以将最新的10条平路的ID放在redis的list集合里面</p>
  > 模拟类似于HttpSession这种需要设定过期时间的功能</p>
  > 发布，订阅消息系统</p>
  > 定时器，计数器</p>
  > 等等

+ ### 去哪下

  > http://redis.io</p>
  > http://www.redis.cn/

+ ### 怎么玩

  > 数据类型，基本操作和配置</p>
  > 持久化和配置,RDB/AOF</p>
  > 事务的控制</p>
  > 主从复制</p>
  > ...

+ ### redis

    + #### 安装
        ...
        
    + #### 杂项知识

        0. 命令查询 【 http://redisdoc.com | help@String/Hash | help <command>】
        1. 单进程
        2. 默认16个数据库，类似数组下标从零开始，初始化默认使用零号库。
        3. select 命令切换选择库
        4. dbsize 查看当前数据库key数量
        5. flushdb 清空当前库
        6. flushall 清空全部(16)库
        7. 统一密码管理，16个库都使用同样的密码。
        8. redis索引都是从0开始
        9. 6379 MERZ取自意大利歌女Alessia Merz的名字 

    + #### redis 配置文件

        * ##### 文件位置
        > 解压完redis.*.tar之后会进入redis dir进行make && make install，在解压目录里面有原配置文件redis.conf,一般会备份使用 

        * ##### Units单位
        > 尽量制定后缀，比如 1k,5GB,4M 等，注意：1k ==> 1000 bytes， 1kb ==> 1024 bytes,另外不区分大小写

        * ##### INCLUDE
        > include /path/to/local.conf <br>
        > 一个标准模版对所有的redis服务器生效的话，其他不用变，将这个文件包含进来就可以。包含文件可以包含其他文件，<br>
        > 注意 include 选项不会被命令【config rewrite】或者哨兵重写<br>
        > redis总是使最后一个配置生效，所以，配置文件放置的位置可以自行决定。

        * ##### GENERAL
        > 通用配置<br>
        > daemonize yes|no    【默认redis没有以守护进程运行<br>
        > pidfile /var/run/redis.pid  【以守护进程运行的时候可以制定一个pid文件<br>
        > port 6379 redis 【运行默认端口6379<br>
        > tcp-backlog 511 【在高并发环境中需要设置一个高值来避免客户端连接较慢的问题，Linux会截断为somaxconn(128)配合<br>
        > timeout 0 【客户端空闲多少秒后关闭链接，0为禁用<br>
        > tcp-keepalive 0  【合理值为60s 使用SO_KEEPALIVE发送一个 tcp ack <br>
        > loglevel notice|debug|verbose|warning  【日志级别<br>
        > logfile "" 【制定日志名<br>
        > databases 16 【制定数据库数量，使用select 切换
        
        * ##### SNAPSHOTTING
        > save <seconds> <changes> 【注释所有save指令 或者 增加 save "" 指令来禁用RBD <br>
        > 【will save the DB if both the given number of seconds and the given number of write operations against the DB occurred 【如果秒数和针对数据库写操作数都出现的话将保存数据库 <br>
        > 【In the example below the behaviour will be to save <br>
        > save 900 1  【after 900 sec (15 min) if at least 1 key changed <br>
        > save 300 10 【after 300 sec (5 min ) if at least 10 keys changed <br>
        > save 60 10000 【after 60 sec if at least 10000 keys changed <br>
        > <br>
        > stop-writes-on-bgsave-error yes 【如果后台进程备份失败，则redis将不接受写入操作，恢复以后就可以写入了. <br>
        > rdbcompression yes 【对于存储到磁盘中的快照，设置是否需要压缩 <br>
        > rdbchecksum yes 【在存储快照后，还可以让redis进行数据校验，会增加10%性能消耗 <br>
        > dbfilename "" 【dump文件名 <br>
        > dir ./  【指定备份目录，默认当前启动redis的pwd
        
        * ##### REPLICATION
        > 
        
        * ##### SECURITY
        > 默认都是无需验证的，可以通过config set requirepass '123' 来临时设置密码，但是每次输入民营都需要验证(auth 123) <br>
        > 上面的无需重启redis, 还有一个种写入配置文件(requirepass '123')，重启生效，永久生效
        
        * ##### LIMITS
        > maxclients 10000  <br>
        > ...

    + #### 数据类型

        * ##### redis五大数据类型: [Introduction to Redis data types(官网介绍)](https://redis.io/topics/data-types-intro )
        
            1. String(字符串)
            2. Hash(哈希类似Java中的map)
            3. List(列表)
            4. Set(集合)
            5. Zset(sorted set:有序集合)
            
        * ##### redis 数据类型使用
        
            | index                   | case                                                         | comment                                                      |
            | ----------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
            | key                     | keys * <br> exists keys<br> move key db<br> expire key <br> ttl key <br> type key | 查找所有的key<br> 判断某个key是否存在 <br>移除当前库的key 到制定的库<br> 为制定的key 设置过期时间<br>查看还有多少秒过期，-1永不过期，-2已过期<br>查看key 对应value类型 |
            | String                  | set/get/del/append/strlen <br> incr/decr/incrby/decrby <br> getrange/setrange <br> setex/setnex(set if no exist) <br> mset/mget/msetnx <br> getset<br> | 设置/获取/删除/追加/长度 <br> 单步增加/减少，和步长增加/减少(只是针对数字) <br> 根据range取值和设值 <br> 设置有expire的值 和 不存在的情况设值 <br> m表示more <br> 先get再set(设置新值，返回旧值) |
            | List                    | lpush/rpush/lrange <br> lpop/rpop <br> lindex <br> llen <br> lrem key n v <br> ltrim key index0,index1 <br> rpoplpush list01 list02 <br> lset key index value <br> linsert key before/after v1 v2 | 从左右进入list,和从左边取范围值 <br> 左右弹值（单个） <br> 按照索引下标获得元素（从0开始） <br> 获取list的长度 <br> 从key中删除n个v (1,2,2,2,3,4)--lrm-(2,2)->(1,2,3,4) <br> 将key对应list 截断（0，4）是5个值 <br> 从list01底部弹一个插入到list02头部 <br> 给一个key制定位置设置值 <br> 在key中v1之前/之后插入v2 |
            | Set                     | sadd/smembers/sismember <br> scard <br> srem key value  <br> srandmember key  <br> spop key  <br> smove key1 key2 v1 <br> sdiff set01 set02<br> sinter set01 set 02 <br> sunion set01 set02 | 添加值（去重）/查看所有成员/判断某个值是否在set中<br> 获取set里面元素的个数 <br> 删除集合中的元素 <br> 随机获取集合中的某个值 <br> 因为是set,所以随机弹出一个 <br> 将key1中的值v1移动到key2 <br> 以第一个(set01)为基准，求差集 <br> 求交集 <br> 就并集 |
            | Hash                    | hset/hget/hmset/hmget/hgetall/hdel <br> hlen <br> hexists key k1 <br> hkeys/hvals <br> hincrby/hincrbyfloat <br> hsetnx | 单个多个/取值设值/取全部(包括键值)/删除key中某个值 <br> 求hash长度 <br> 判断时候存在某个key <br> 取全部的键/值 <br> hash中某个增加步长，有小数用float <br> hash中不存在了再添加 |
            | Zset<br>(set+<br>score) | zadd/z[rev]range/zrange withsocres <br> zrangebyscore key (sc1 sc2  [(,limit] <br> zrevrangebyscore key (sc1 sc2 <br> zrem key v1 <br> zcard/zcount/zrank/zrevrank/zscore | 添加/取范围/反范围/带分数范围<br> zrange根据index,这个是byscore <br> 反向zrangebyscore <br> 删除zset 中的v1值 <br> 总个数/范围内个数/下标/反向下标/对饮的score |
        
    + #### redis持久化

        + ##### RDB(Redis DataBase)
          
            1. ###### [官网介绍](https://redis.io/topics/persistence )
            > https://redis.io/topics/persistence
            
            2. ###### 定义
            > 在制定的时间间隔内，将内存中的数据集快照写入磁盘，也就是将Snapshot快照，<br>
            > 它恢复时是将快照文件直接读到内存中。<br>
            > Redis会单独创建（fork)一个子进程来进行持久化，会现将数据写入到一个临时文件中，待持久化过程都结束了（也就是触发了save），再用这个临时文件替换上次持久化好的文件。整个过程中，主进程是不进行任何IO操作的，这就确保了极高的性能。如果需要进行大规模数据的恢复，且对数据恢复完整性不是很敏感的化，那RDB方式要比AOP更加高效。RDB的缺点是最后一次还未触发的数据会丢失 <br>
            > 另外fork进程会增加操作系统的负担。
            
            3. ###### 如何触发RDB快照
            > 配置文件中默认的快照配置 【冷拷贝后重新使用，即复制到别的机器 <br>
            > 命令save（阻塞备份） 或者是 bgsave（后台备份）lastsave（获取最后一次成功执行的时间） <br>
            > 执行flushall命令，也会产生dump.rdb文件，但里面是空的，无意义
            
            4. ###### 优势
            > 适合大规模的数据恢复 <br>
            > 对数据完整性和一致性的要求不高
            
            5. ###### 劣势
            > 最后一次数据丢失 <br>
            > fork的时候，内存中的数据被克隆了一份，大致2倍的膨胀性需要考虑
            
            6. ###### 如何停止
            
            > redis-cli config set save "" 【动态停止RDB保存
            
        + ##### AOF（append.......)

    + #### redis事务

    + #### redis发布订阅

        - ##### 定义
            > 进程间的一种消息通信模式，发送者发送消息，订阅者接受消息
        
        - ##### 图列
            > 下图展示了channel1 和订阅它的三个客户端
            
            ```mermaid
            graph BT
            
            client2--sub-->id2((channel1))
            client4--sub-->id2((channel1))
            client1--sub-->id2((channel1))
            
            ```
            > 当有新消息通过PUBLISH命令发送给频道channel1时，这个消息就会被发送给订阅它的三个客户端
            
            ```mermaid
            graph TB
            id3>PUBLISH channel1 message]-.send.->id2((channel1))
            id2((channel1))-.pub.->client2
            id2((channel1))-.pub.->client4
            id2((channel1))-.pub.->client1
            ```
        
        - ##### 命令
            > SUBSCRIBE c1 c2 c3 【一次性订阅多个 <br>
            > PUSBLISH c2 hello-redis 【消息发布 <br>
            > SUBCSCRIBE new* 【订阅多个使用通配符* <br>
            > PUBLISH new1 redis2021  【发布消息
    
    + #### 淘汰策略
        |index|opt|
        |---|---|
        | noeviction      | 不进行数据淘汰，也是Redis的默认配置。这时，当缓存被写满时，再有写请求进来，Redis不再提供服务，直接返回错误。|
        | volatile-ttl    | 缓存满了之后，会针对设置了过期时间的键值对中，根据过期时间的先后顺序进行删除,越早过期的越先被删除。|
        | volatile-random | 缓存满了之后，在设置了过期时间的键值对中进行随机删除。|
        | volatile-lru    | 缓存满了之后，针对设置了过期时间的键值对，采用LRU算法进行淘汰。最近最少使用 |
        | volatile-lfu    | 缓存满了之后，针对设置了过期时间的键值对，采用LFU的算法进行淘汰。经常最少使用 |
        | allkeys-random  | 缓存满了之后，从所有键值对中随机选择并删除数据。|
        | allkeys-lru     | 缓存满之后，使用LRU算法在所有的数据中进行筛选删除。|
        | allkeys-lfu     | 缓存满了之后，使用LFU算法在所有的数据中进行筛选删除。|

    + #### zset实现：
        > 小于阈值 用 ziplist, 当不满足以下任意条件时，使用skiplist
        - zset-max-ziplist-entries 128 //元素个数超过128，将用skiplist编码
        - zset-max-ziplist-value 64 //单个元素大小超过64byte，将用skiplist编码

    + #### 缓存问题
        - ##### 缓存穿透
            > 用户构造恶意数据，直逼数据库。
            > 解决： 参数校验，缓存空值

        - ##### 缓存击穿
            > 大量访问同时到来 的时候缓存失效，直逼数据库
            > 解决： 加锁，自动续期，不让热点数据过期手动维护。

        - ##### 缓存雪崩
            > 多个缓存击穿，redis服务器宕机
            > 过期值加随机数，高可用。

    + #### 集群模式
        * 主从复制
            - 两种工作模式
                + 全量复制
                + 增量复制
            - 三种工作方式
                + 一主二从 A <--- B , A <--- C
                + 薪火相传 A <--- B <--- C
                + 反客为主 salve no one
        * Sentinel 哨兵模式
        * cluster 模式

    + #### 哨兵机制
        > redis-sentinel sentinel.conf ,主挂了，剩余选取，主恢复，slave身份加入
        > ​    

## 参考
* [周阳-尚硅谷reids视频教程](https://www.bilibili.com/video/BV1J4411x7U1 )
* [阮一峰-CAP 定理的含义](http://www.ruanyifeng.com/blog/2018/07/cap.html )
* [缓存穿透，击穿，雪崩](https://blog.csdn.net/lzy194/article/details/122231010 )
* [Redis Ziplist（压缩列表）](https://blog.csdn.net/solo_jm/article/details/118520888)
* [理解redis调表](https://www.cnblogs.com/zh718594493/p/12111949.html)
* [redis的MULTI与PIPELINE](https://cloud.tencent.com/developer/article/1757465)
* [redis主从复制原理](https://baijiahao.baidu.com/s?id=1744453281342133616)
* [Redis三种集群模式详解](https://www.jb51.net/article/224568.htm)
* [reids 事务错误图解](https://blog.csdn.net/weixin_37548768/article/details/124538778)
* [缓存穿透、缓存击穿、缓存雪崩的理解和解决方案](https://blog.csdn.net/a898712940/article/details/116212825)