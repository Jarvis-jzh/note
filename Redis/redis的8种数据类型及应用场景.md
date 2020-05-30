## Redis 的 8 种数据类型及应用场景

---

### 前言

> Redis 有多少种数据类型？
>
> 参考：[马士兵教育-周老师](<https://ke.qq.com/course/480335>)、[咕泡学院-青山老师](<https://www.gupaoedu.com/>)

相信小伙伴们对这个问题是非常的熟悉了，肯定很多小伙伴第一反应就是 5 种，我一开始也以为是 5 种，自从听了青山老师的课后才恍然大悟，原来 Redis 有 8 种数据类型。

那么，结合两位老师的经验，再通过~~百度~~官网收集资料，来给大家分享一波 Redis 的数据类型及应用场景。



### 简介

**Redis是一个开源的，基于内存的数据结构存储系统，它可以作为数据库、缓存、消息中间件。**

同时支持多种数据类型：

- String
- List
- Hash
- Set
- ZSet
- HyperLogLog
- Geo
- Stream



### String

String 是二进制安全的，同时它与 JAVA 中的 Spring 不同，它更像是一个泛型，实际上它不仅仅是只能用来存储我们的字符串，还可以用来存储数字类型和二进制位。

- 字符串
- 数字类型
- 二进制位（BitMap）



#### 字符串

常见操作

| 格式                 | 作用                                           |
| -------------------- | ---------------------------------------------- |
| SET key value        | 存入字符串键                                   |
| SETNX key value      | 存入一个不存在的字符串，若存在 key，存储不成功 |
| GET key              | 获取一个字符串键                               |
| MSET key value [k v] | 批量存储字符串键                               |
| MGET key [k ...]     | 批量获取字符串键                               |

其中 setnx 命令是原子操作，可以作为分布式锁的解决方案



应用场景：缓存、session共享、分布式锁、fs 文件系统（针对一些很小的文件）



#### 数字类型

常见操作

| 格式                 | 作用                               |
| -------------------- | ---------------------------------- |
| INCRBY key increment | 对数字 key 进行 {increment} 的增加 |
| DECRBY key decrement | 对数字 key 进行 {decrement} 的减少 |
| INCR key             | 对数字 key 自增 1                  |
| DECR key             | 对数字 key 自减 1                  |

应用场景：计数器（文章阅读量、点赞数）、限流（以访问者 IP 和其它信息为 Key，限制访问次数）



#### 二进制位（BitMap）

常见操作

| 格式                       | 作用                                           |
| -------------------------- | ---------------------------------------------- |
| GETBIT key offset          | 获取指定偏移量上的位（bit）值                  |
| SETBIT key offset value    | 设置或替换指定偏移量上的位（bit）值            |
| BITCOUNT key [start] [end] | 统计从 Start 到 End 被设置为 1 的 bit 位的数量 |

BitMap 是对一个字符串的位进行操作，然后 1 个字节等于 8 个位（1 Byte = 8 Bit）。那么它有什么用呢？最常见的应用场景就是在线用户统计，我们以 0 表示用户下线，1 表示用户在线，那么 1 个字节就能表示 8 个用户的当前在线信息，1 KB 就能表示 8192 个用户在线信息，而且占用空间还非常小。



应用场景：在线用户统计、用户签到、活跃用户统计



### List

常见操作

| 格式                        | 作用                                                         |
| --------------------------- | ------------------------------------------------------------ |
| LPUSH key value [value ...] | 往key的列表键中左边放入一个元素，key不存在则新建             |
| RPUSH key value [value ...] | 往key的列表键中右边放入一个元素，key不存在则新建             |
| LPOP key                    | 从 key 的列表键最左端弹出一个元素                            |
| RPOP key                    | 从 key 的列表键最右端弹出一个元素                            |
| LRANGE key start stop       | 获取列表键从 start 下标到 stop 下标的元素                    |
| BLPOP key [key ...] timeout | 阻塞的从key的列表键最左端弹出一个元素，若列表键中不存在元素，阻塞等待 {timeout} 秒，若 {timeout}=0，一直阻塞 |
| BRPOP key [key ...] timeout | 阻塞的从key的列表键最右端弹出一个元素，若列表键中不存在元素，阻塞等待 {timeout} 秒，若{timeout}=0，一直阻塞 |

根据这些命令，我们可以用 list 做栈、队列、数组，因此，所有使用这些数据结构的应用场景都可以用 redis 的 list 代替。



应用场景：评论列表、消息队列



### Hash

常见操作

| 格式                                    | 作用                       |
| --------------------------------------- | -------------------------- |
| HSET key field value                    | 存储一个散列键             |
| HSETNX key field value                  | 存储一个不存在的散列键     |
| HMSET key field value [field value ...] | 在一个 key 中存储多个 field |
| HGET key field                          | 获取 key field 的散列键值  |
| HMGET key field [field ...]             | 批量获取 key 中多个field的值 |
| HDEL key field [field ...]              | 删除散列键 key 中field的值 |
| HINCRBY key field increment | 对key散列中 field 进行数字操作 {increment} |

应用场景：聚集数据、详情页



### Set

常见操作

| 格式                         | 作用                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| SADD key member [member ...] | 往集合键 key 中存放元素，若key不存在则新建                   |
| SREM key member [member ...] | 从集合键 key 中删除元素                                      |
| SMEMBERS key                 | 获取集合键 key 中所有的元素                                  |
| SCARD key                    | 获取集合键 key 的元素个数                                    |
| SISMEMBER key member         | 判断 {member} 元素是否存在于集合键 key 中                    |
| SRANDMEMBER key [count]      | 从集合键 key 中选出 {count} 元素，不从集合键 key 中删除。{count} 为正时，返回随机不重复元素；为负时，返回随机可重复元素 |
| SPOP key [count]             | 从集合键 key 中选出 {count} 元素，并且从集合键 key 中删除    |
| SUNITON key [key...]         | 并集，把多个 key 下的元素都放到同一个结果集里                |
| SINTER key [key...]          | 交集，把多个 key 下的相同元素放到同一个结果集里              |
| SDIFF key [key...]           | 差集，返回第一个 key 的差集（以第一个 key 为主）             |

应用场景：好友推荐（并集是共同好友）、商品筛选 



### ZSet

常见操作

| 格式                                      | 作用                                                         |
| ----------------------------------------- | ------------------------------------------------------------ |
| ZADD key score element [..]               | 往有序集合键 key 中存放元素，若 key 不存在则新建             |
| ZREM key element [element ..]             | 从有序集合 key 中删除元素                                    |
| ZSCORE key element                        | 获取有序集合 key 中 {element} 元素的 score 值                |
| ZINCRBY key increment element             | 给有序集合 key 中 {element}元素进行 score 值操作，若 key 不存在则新建， {element} 元素不存在则新增后进行 score 值操作 |
| ZCARD key                                 | 获取有序集合 key 中元素个数                                  |
| ZRANGE key start stop [WITHSCORES]        | 正序获取有序集合 key 从 start 下标到 stop 下标的元素         |
| ZREVRANGE key start stop [WITHSCORES]     | 倒序获取有序集合 key 从 start 下标到 shop 下标的元素         |
| ZUNIONSTORE destkey numkeys key [key ...] | 并集计算                                                     |
| ZINTERSTORE destkey numkeys key [key ...] | 交集计算                                                     |

应用场景：排行榜



### HyperLogLog

> 来自：[菜鸟教程 - Redis HyperLogLog](<https://www.runoob.com/redis/redis-hyperloglog.html>)
>
> Redis 在 2.8.9 版本添加了 HyperLogLog 结构。
>
> Redis HyperLogLog 是用来做基数统计的算法，HyperLogLog 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定 的、并且是很小的。
>
> 在 Redis 里面，每个 HyperLogLog 键只需要花费 12 KB 内存，就可以计算接近 2^64 个不同元素的基数。这和计算基数时，元素越多耗费内存就越多的集合形成鲜明对比。
>
> 但是，因为 HyperLogLog 只会根据输入元素来计算基数，而不会储存输入元素本身，所以 HyperLogLog 不能像集合那样，返回输入的各个元素。

| 格式                                      | 作用                                          |
| ----------------------------------------- | --------------------------------------------- |
| PFADD key element [element ...]           | 添加指定元素到 HyperLogLog 中                 |
| PFCOUNT key [key ...]                     | 返回指定的 key 的基数估算值                   |
| PFMERGE destkey sourcekey [sourcekey ...] | 将多个 HyperLogLog 合并为一个新的 HyperLogLog |

由于其特点，如果数据量不是特别的大的话，使用会有点大材小用（12 KB 可以计算 2^64 个不同元素的基数）。同时它也有局限性，就是只能统计基数数量，而没办法知道具体的内容，所以 HyperLogLog 一般用在统计方面的场景。

应用场景：统计每日访问 IP 数



### Geo

在 Redis 3.2 版本中推出的 Geo 数据类型，可以用来储存地理位置信息，并对这些信息进行操作。

具体内容可以看官网[官网](<http://www.redis.cn/commands/georadius.html>)

| 格式                                                         | 作用                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| GEOADD key longitude latitude member [longitude latitude member ...] | 往 key 中添加地理位置信息                                    |
| GEODIST key member1 member2 [m\|km\|ft\|mi]                  | 返回 key 中指定两个成员之间的距离                            |
| GEOHASH key member [member ...]                              | 返回 key 中指定成员的 geohash                                |
| GEOPOS key member [member ...]                               | 返回 key 中指定成员的经纬度信息                              |
| GEORADIUS key longitude latitude radius m\|km\|ft\|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count] | 以给定位置为中心，半径不超过给定半径的附近所有位置           |
| GEORADIUSBYMEMBER key member radius m\|km\|ft\|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count] | 和[GEORADIUS](http://www.redis.cn/commands/georadius.html)相似，只是中心点不是指定经纬度，而是指定已添加的某个位置作为中心 |

应用场景：附近的人



### Stream

Redis 5.0 添加的数据类型。具体内容可以看官网[Redis Streams 介绍](<http://www.redis.cn/topics/streams-intro.html>)。



| 格式                                                         | 作用                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| XACK key group ID [ID ...]                                   | 从消费者组的*待处理条目列表*（简称`PEL`）中删除一条或多条消息。 |
| XADD key ID field value [field value ...]                    | 将数据添加到`key`中，如果`id`参数是字符`*`，则`XADD`命令会自动生成一个唯一 ID。 |
| XCLAIM key group consumer min-idle-time ID [ID ...] [IDLE ms] [TIME ms-unix-time] [RETRYCOUNT count] [force] [justid] | 更改消费组中的消息所有权                                     |
| XDEL key ID [ID ...]                                         | 删除指定条目                                                 |
| XGROUP [CREATE key groupname id-or-\$] [SETID key groupname id-or-$] [DESTROY key groupname] [DELCONSUMER key groupname consumername] | 创建，销毁，管理消费组。                                     |
| XINFO [CONSUMERS key groupname] [GROUPS key] [STREAM key] [HELP] | 用于检索关于流和关联的消费者组的不同的信息。                 |
| XLEN key                                                     | 返回流中的条目数。如果指定的`key`不存在，则此命令返回0。     |
| XPENDING key group [start end count] [consumer]              | 返回消费组中挂起条目列表的信息和条目。                       |
| XRANGE key start end [COUNT count]                           | 返回流中满足给定`ID`范围的条目。范围由最小和最大`ID`指定。所有`ID`在指定的两个`ID`之间或与其中一个`ID`相等（闭合区间）的条目将会被返回。 |
| XREAD [COUNT count] [BLOCK milliseconds] STREAMS key [key ...] id [id ...] | 从一个或者多个流中读取数据，仅返回`ID`大于调用者报告的最后接收`ID`的条目。 |
| XREADGROUP GROUP group consumer [COUNT count] [BLOCK milliseconds] [NOACK] STREAMS key [key ...] ID [ID ...] | 使用消费组从流中返回新条目，或者访问给定消费者的挂起条目的历史记录。会阻塞。 |
| XREVRANGE key end start [COUNT count]                        | 与`XRANGE`相比，返回流中的一系列元素，其`ID`与指定的`ID`间隔相匹配，顺序相反(从大到小`ID`) |
| XTRIM key MAXLEN [~] count                                   | `XTRIM`将流裁剪为指定数量的项目，如有需要，将驱逐旧的项目（ID较小的项目）。此命令被设想为接受多种修整策略，但目前只实现了一种，即`MAXLEN`，并且与`XADD`中的`MAXLEN`选项完全相同。 |

应用场景：消息队列