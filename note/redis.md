#### **redis-benchmark 压力测试工具**

![](../%E5%B7%A5%E5%85%B7/redis_1.png)



### 基础知识

redis默认有16个数据库，默认使用第0个数据库，可用select进行切换（select 3 ，切换至第三个数据库）

```bash
select 3 #切换至第三个数据库
DBSIZE #查看db大小

set key value #设置key-value
get key #根据key获取value
keys * #查看所有的key
EXISTS key #判断key是否存在
move key 数据库号 #移除数据库中的key
EXPIRE key 过期时间（秒） #设置key的过期时间
type key #查看当前key的类型


flushdb #清除当前数据库
FLUSHALL #清除全部数据库的内容
```

### 五大数据类型

#### **String**

```bash
APPEND key 字符串 #在key的值后追加字符串
STRLEN key #获取字符串的长度
incr key #自增一
decr key #自减一
INCRBY key num #增加num
DECRBY key num #减少num
GETRANGE key num1 num2 #查看从num1到num2的字符串
GETRANGE key 0 -1 #查看全部字符串
SETRANGE key num 字符串 #从num处修改字符串
setex key 过期时间 value #设置key过期时间（set with expire）
setnx key value #key不存在再设置(set if not exist)
mset k1 v1 k2 v2 k3 v3 #一次性设置多个key-value
mget k1 k2 k3 #一次性获取多个值
msetnx k1 v1 k2 v2 #key不存在再设置,如果有一个存在就失败，原子性操作，要么一起成功，要么一起失败

#对象
set user:1{name:zhangsan,age:3} #设置一个user:1对象 值为json字符来保存一个对象
mset user:1:name zhangsan user:1:age 3
mget user:1:name user:1:age

getset key value #先get再set 如果不存在值，则返回nil,如果存在值，获取原来的值，并设置新值
```

#### **List**

```bash
 LPUSH list名 value #将一个值或者多个值插入到列表的头部（左）
 LRANGE list名 num1 num2 #获得num1 num2之间的值
 LRANGE list名 0 -1 #获取全部的值
 RPUSH list名 value #将一个值或者多个值插入到列表的尾部（右）
 LPOP list名 #移除list的第一个元素
 RPOP list名 #移除list的最后一个元素
 lindex list名 num #通过下标获得list的某一个值
 Llen list名 #返回列表的长度
 Lrem list名 num value #移除list中指定数目的value,精确匹配
 ltrim list名 num1 num2 #截取list num1 num2之间的值，list被截断了
 rpoplpush list1 list2#移除列表list1的最后一个元素，并添加到新的列表list2
 EXISTS list名 #判断list是否存在
 lset list名 num value #将list中指定下标num的值替换为value
 linsert list名 before|after value1 value2 #在list的value1的前|后插入value2的值
```

#### **Set**

```bash
sadd set名 value #向set中添加值
SMEMBERS set名 #查看set所有值
SISMEMBER set名 value #判断某一个值是否在set中
scard set名 #获取set中的元素个数
srem set名 value #移除set中的元素
srandmember set名 #随机抽选出一个元素
srandmember set名 num #随机抽选出num个元素
spop set名 #随机删除set中的元素
smove set1 set2 value #将value从set1移到set2
sdiff set1 set2 #差集
sinter set1 set2 #交集
sunion set1 set2 #并集
```

#### **Hash**

hash适合存储经常变动的信息，更适合对象的存储

```bash
hset hash名 field名 value #set一个具体的key-value
hget hash名 field名 #获取一个字段值
hmset hash名 field1 value1 field2 value2 #set多个key-value
hmget hash名 field1 field2 #获取多个字段值
hgetall hash名 # 获取全部的数据
hdel hash名 field1 #删除hash指定的key字段，对应的value也就消失了
hlen hash名 #获取hash的field字段数量
hexists hash名 field1 #判断hash中的指定字段是否存在
hkeys hash名 #只获得所有field
hvals hash名 #只获得所有value
hincrby hash名 field名 num #增加num
hdecrby hash名 field名 num #减少num
hsetnx hash名 field名 value #如果不存在则可以设置，如果存在则不能设置

```

#### **Zset**（有序集合）

在set的基础上，增加了一个值，set  v1  zset score1 v1

```bash
zadd zset名 score value #添加一个值
zadd zset名 score1 value1 score2 value2 #添加多个值
zrange zset名 num1 num2 #从小到大排序,获取下标为num1到num2之间的值
zrangebyscore zset名 -inf +inf #显示全部的用户，从小到大
zrangebyscore zset名 -inf +inf withscores #显示全部的用户,并且附带score
zrangebyscore zset名 -inf num withscores #显示小于num的值
zrevrange zset名 0 -1 #从大到小进行排序
zrevrange zset名 num1 num2 #从大到小进行排序,获取下标为num1到num2之间的值
zrem zset名 value #移除有序集合中的指定元素
zcard zset名 #获取有序集合中的个数
zcount zset名 num1 num2 #获取指定区间的成员数量
```

### 三种特殊数据类型

#### geospatial 地理位置

geo底层实现原理是zset，可以使用zset命令来操作geo

```bash
geoadd key 经度 纬度 #添加地理位置，两极无法添加
geopos key #获取指定城市经纬度
geodist key1 key2 [单位] #查看key1、key2的直线距离，单位：m表示米，km表示千米，mi表示英里，ft表示英尺
georadius key 经度 纬度 num [单位] #以指定经纬度为中心，查询指定num单位内的key
georadius key 经度 纬度 num [单位] withdist #显示到中间点的位置和距离
georadius key 经度 纬度 num [单位] withcoord #显示他人的定位信息
georadius key 经度 纬度 num [单位] withdist withcoord count num #筛选出指定num的结果
georadiusbymember key num [单位] #找出位于指定元素周围的其他元素
geohash key1 key2 #将二维的经纬度转换为一维的字符串，如果两个字符串越接近，那么距离越近 
```

#### hyperloglog 基数统计

优点：占用的内存是固定的，2^64不同的元素的技术，只需要12KB内存

0.81%错误率，如果y允许容错，可以使用hyperloglog，如果不允许容错，就使用set或者自己的数据类型即可

```bash
pfadd key value1 value2... #创建一组元素
pfcount key #统计key元素的基数数量
pfmerge key1 key2 key3 #合并两组 key2 key3=>key1 并集
```

#### bitmaps

位存储，操作二进制位进行记录，就只有0和1两个状态，统计用户信息，活跃，不活跃，登录，未登录，两个状态的，都可以使用bitmaps,

```bash
setbit key offset 0|1 #设置offset处的值
getbit key offset #获取offset处的值
bitcount key #统计key中1的数量
```

### 事务

redis事务本质：一组命令的集合，一个事务中的所有命令都会被序列化，在事务执行的过程中，会按照顺序执行

redis单条命令是保存原子性的，但是事务不保证原子性

redis事务没有隔离级别的概念，所有的命令在事务中，并没有直接的执行，只有发起执行命令的时候才会执行

redis事务：

+ 开启事务（multi）
+ 命令入队（....）
+ 执行事务（exec）

```bash
mult #开启事务
exec #执行事务
discard #取消事务
```

> **编译型异常（代码有问题，命令有错），事务中所有的命令都不会执行**



> **运行时异常，如果事务队列中存在语法性，那么执行命令的时候，其他命令是可以正常执行的，错误命令抛出异常**

#### 监控

**悲观锁**：无论做什么都会加锁

**乐观锁**：不会上锁，更新数据的时候去判断，在此期间是否有人修改过这个数据，获取version，更新时比较version

> **redis监视检测**

```bash
watch key #监视key，可以当乐观锁用
unwatch #取消监视
```



### jedis

### redis.conf详解

```bash
#网络
bind 127.0.0.1 #绑定的ip
protected-mode yes #保护模式
port 6379 #端口设置

#通用 general
daemonize yes #以守护进程的方式运行，默认是no,需要自己开启为yes
pidfile/var/run/redis_6379.pid #如果以后台方式运行，需要指定一个pid文件

#日志
# Specify the server verbosity level.
# This can be one of:
# debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably) 生产环境
# warning (only very important / critical messages are logged)
loglevel notice
logfile "" #日志的文件位置名
database 16 #数据库的数量，默认是16个数据库
always-show-logo yes #是否显示logo

#快照
#持久化，在规定的时间内，执行了多少次操作，则会持久化到文件 .rdb .aof
#redis是内存数据库，如果没有持久化，那么数据断电即失

#900秒内，如果至少只有一个key进行了修改，就进行持久化操作
save 900 1
#300秒内，如果至少10个key进行了修改，就进行持久化操作
save 300 10
#60秒内，如果至少10000个key进行了修改，就进行持久化操作
save 60 10000

stop-writes-on-bgsave-error yes #持久化如果出错，是否还需要继续工作

rdbcompression yes #是否压缩rdb文件，需要消耗一些cpu资源

rdbchecksum #保存rdb文件的时候，进行错误的检查校验

dir ./ #rdb 文件保存的目录

#REPLICATION复制

#SECURITY 安全
#可以设置redis密码，默认没有密码
requirepass 密码

#限制 CLIENTS
maxclients 10000 #设置能连接上redis的最大客户端的数量
maxmeomory <bytes> #redis配置最大的内存容量
maxmemory-policy noeviction #内存达到上限后的处理策略
1.volatile-lru: 只对设置了过期时间的key进行LRU（默认值）
2.allkeys-lru: 删除lru算法的key
3.volatile-random: 随机删除即将过期的key
4.allkeys-random: 随机删除
5.volatile-ttl:删除即将过期的
6.noeviction: 永不过期，返回错误

#APPEND ONLY模式 aof配置
appendonly no #默认是不开启aof模式的，默认是使用rdb方式持久化的，在大部分情况下rdb完全够用！
appendfilename "appendonly.aof" #持久化的文件的名字
#appendfsync always #每次修改都会sync,消耗性能
appendfsync everysec #每秒执行一次sync,可能会丢失这1s的数据
#appendfsync no #不执行sync,这个时候操作系统自己同步数据，速度最快

```

### redis持久化

##### RDB

![](../%E5%B7%A5%E5%85%B7/redis_2-1612238074778.png)

在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是行话讲的snapshot快照，他恢复时是将快照文件读到内存里。

redis会单独创建（fork）一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。整个过程中，主进程不需要进行任何IO操作。这就确保了极高的性能。如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加高效。RDB的缺点是最后一次持久化后的数据可能丢失。我们默认的就是RDB，一般情况下不需要修改这个配置！

**rdb保存的文件是dump.rdb 都是在配置文件快照中进行配置的**

**触发机制**

1. save的规则满足的情况下，会自动触发rdb规则
2. 执行flushall命令，也会触发我们的rdb规则
3. 退出redis,也会产生rdb文件

备份就会自动生成dump.rdb

**如果恢复rdb文件**

1. 只需要将rdb文件放在我们redis启动目录就可以，redis启动的时候会自动检查dump.rdb恢复其中的数据

2. 查看需要存在的位置

   ```bash
   config get dir
   #"dir"
   #"/usr/local/bin" #如果在这个目录下存在dump.rdb文件，启动就会自动恢复其中的数据
   ```

   优点：

   1. 适合大规模的数据恢复
   2. 对数据的完整性要求不高

   缺点：

   1. 需要一定的时间间隔进程操作！如果redis宕机了，这个最后一次的修改数据就没有了
   2. fork进程的时候，会占用一定的内容空间

### AOF(Append Only File)

将我们的所有命令都记录下来,history,恢复的时候就把则个文件全部在执行一边

![](../%E5%B7%A5%E5%85%B7/redis_3.png)

以日志的形式记录每个写操作，将redis执行过的所有指令记录下来（读操作不记录），只许追加文件但不可以改写文件，redis启动之初会读取该文件重新构建数据，换言之，redis重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作。

**aof保存的是appendonly.aof文件**

默认不开启aof,需要手动将配置文件appendonly改为yes,重启redis就可以生效了

如果aof文件有错位，这时候 redis是启动不起来的，我们需要修复这个文件，redis给我们提供了一个工具 **redis-check-aof --fix**

优点：

1. 每一次修改都同步，文件的完整性会更好
2. 每秒同步一次，可能会丢失一秒的数据
3. 从不同步，效率最高的！

缺点：

1. 相对于数据文件来说，aof远远大于rdb，修复的速度也比rdb慢！
2. aof运行效率也比rbd慢，所以redis默认的配置就是rdb持久化

### redis发布订阅

redis发布订阅（pub/sub）是一种消息通信模式：发送者（pub）发送消息，订阅者（sub）接收消息。

redis客户端可以订阅任意数量的频道

订阅/发布消息图：

![](../%E5%B7%A5%E5%85%B7/redis_4.png)

```bash
SUBSCRIBE channel... #订阅给定的一个或多个频道的信息
UNSUBSCRIBE channel... #退订给定的频道
PSUBSCRIBE pattern... #订阅一个或多个符合给定模式的频道
PUNSBUSCRIBE pattern... #退订所有给定模式的频道
PUBSUB subcommand argument... #查看订阅和发布系统状态
PUBLISH channel message #将消息发送到指定的频道
```

### redis主从复制

**概念**

主从复制，是指将一台redis服务器的数据，复制到其他的redis服务器。前者称为主节点（master/leader）后者称为从节点（slave/follower）;数据的复制是单向的，只能有主节点到从节点，master以写为主，slave以读为主。

默认情况下，每台redis服务器都是主节点；且一个主节点可以有多个从节点（或没有从节点），但一个从节点只能有一个主节点。

**主从复制的作用主要包括：**

1. 数据冗余：主从复制实现了数据的热备份，是持久化之外的一种数据冗余方式。
2. 故障恢复：当主节点出现问题时，可以由从节点提供服务，实现快速的故障恢复；实际上是一种服务的冗余
3. 负载均衡：在主从复制的基础上，配合读写分离，可以由主节点提供写服务，由从节点提供读服务（即写redis数据时应用连接主节点，读redisdis数据时应用连接从节点），分担服务器负载；尤其是在写少读多的场景下，通过多个从节点分担负载，可以大大提高redis服务器的并发量
4. 高可用（集群）基石：除了上述作用以外，主从复制还是哨兵和集群能够实施的基础，因此说主从复制是redis高可用的基础。

一般来说，要将redis运用于工程项目中，值使用一台redis是万万不能的（宕机），原因如下：

1. 从结构上，单个redis服务器会发生单点故障，并且一台服务器需要处理所有的请求负载，压力较大；
2. 从容量上，单个redis服务器内存容量有限，就算一台redis服务器内存容量为256G，也不能将所有内存用作redis存储内存，一般来说，**单台redis最大使用内存不应该超过20G**

**环境配置**

只配置从库，不用配置主库

```bash
info replication #查看当前库的信息
```

复制三个配置文件，然后修改对应信息

1. 端口
2. pid名字
3. log文件名字
4. dump.rdb名字

```bash
SLAVEROF ip地址 端口 #从节点配置主节点
```

**命令配置是暂时的，配置文件配置是永久的**

**主机中的所有信息和数据都会自动被从机保存**

主机断开连接，从机依旧连接到主机，但是没有写操作，这个时候，主机如果回来了，从机依旧可以直接获取到主机写的信息！

如果使用命令行，来配置的主从，这个时候如果重启了，就会变回主机！**只要变回从机，立马就会从主机中获取值！**

> **复制原理**

slave 启动成功连接到master后会发送一个sync同步命令

master收到命令，启动后台的存盘进程，同时收集所有接收到的用于修改数据库集命令，在后台进程执行完毕之后，**master将传送整个数据文件到slave,并完成一次完全同步**。

全量复制：slave服务在接收到数据库文件后，将其存盘并加载到内存中

增量复制：master继续将新的所有收集到的修改命令依次传给slave，完成同步

但是只要重新连接mater，一次完全同步（全量复制）将被自动执行

> **如果没有老大了，这个时候能不能选择一个老大出来呢？手动！**

如果主机断开连接，我们可以使用**SLAVEOF no one** 将自己变成主机！其他的节点就可以手动连接到最新的这个主节点（手动）！如果这个时候老大修复了，那就重新连接！

### 哨兵模式（自动选老大）

哨兵模式是一种特殊的模式，首先redis提供了哨兵的命令，哨兵是一个独立的进程，作为进程，他会独立运行。**其原理是哨兵通过发送命令等待redis服务器响应，从而监控运行的多个redis实例。**

1. 配置哨兵配置文件sentinel.conf

```bash
#sentinel monitor 被监控的名称 host port 1
sentinel monitor myredis 127.0.0.1 6379 1
#后面这个数字1，代表主机挂了，slave投票看让谁接替成为主机，票数最多的，就会成为主机！
```

2. 启动哨兵

```bash
#redis-sesntinel sentinel.conf所在路径
redis-sentinel kconfig/sentinel.conf
```

**如果主机此时回来了，只能归并到新的主机下，当作从机，这就是哨兵模式的规则！**

优点：

1. 哨兵集群，基于主从复制模式，所有主从配置优点，他全有
2. 主从可以切换，故障可以转移，系统的可用性就会更好
3. 哨兵模式是主从模式的升级，手动到自动，更加健壮！

缺点：

1. redis不好在线扩容的，集群容量一旦达到上限，在线扩容就十分麻烦
2. 实现哨兵模式的配置很麻烦，里面有很多选择

### 缓存穿透与雪崩

**缓存穿透**（查不到）

用户想要查询一个数据，发现redis内存数据库中没有，也就是缓存没有命中，于是向持久层数据库查询。发现也没有，于是本次查询失败。当用户很多的时候，缓存都没有命中，于是就去请求了持久层数据库。这会给持久层数据库造成很大的压力，这时候就相当于出现了缓存穿透。

**布隆过滤器**

布隆过滤器是一种数据结构，对所有可能查询的参数以hash形式存储，在控制层先进行校验，不符合则丢弃，从而避免了对底层存储系统的查询压力；

但是这种方法会存在两个问题：

1. 如果空值能够被缓存起来，这就意味着缓存需要更多的空间存储更多的键，因为这当中可能会有很多的空值的键；
2. 即使对空值设置了过期时间，还是会存在缓存层的数据会有一段时间窗口的不一致，这对于需要保持一致性的业务会有影响。

**缓存击穿**（量太大，导致缓存过期）

缓存击穿是指一个key非常热点，在不停的扛着大并发，大并发集中对这一个点进行访问，当这个key在失效的瞬间，持续的大并发就穿破缓存，直接请求数据库。

当某个key在过期的瞬间，有大量的请求并发访问，这类数据一般是热点数据，由于缓存过期，会同时访问数据库来查询最新的数据，并且回写缓存，会导致数据库压力过大。

> **解决方案**

**设置热点永不过期**

从缓存层来看，没有设置过期时间，所以不会出现热点key过期后产生的问题。

**加互斥锁**

分布式锁：使用分布式锁，保证对于每个key同时只有一个线程去查询后端服务，其他线程没有获得分布式锁的权限，因此只需要等待即可。这种方式将高并发的压力转移到了分布式锁，因此对分布式锁的考验很大。

### 缓存雪崩

缓存雪崩，是指在某一个时间段，缓存集中过期失效，redis宕机。

> 解决方案

**redis高可用**

这个思想的含义是，既然redis有可能挂掉，那我多增设几台redis，这样一台挂掉之后其他的还可以继续工作，其实就是搭建的集群（异地多活）

**限流降级**

这个解决方案的思想是，在缓存失效后，通过加锁或者队列来控制读数据库写缓存的线程数量。比如对某个key只允许一个线程查询数据和写缓存，其他线程等待。

**数据预热**

数据加热的含义就是在正式部署之前，我先把可能的数据先预先访问一遍，这样部分可能大量访问的数据就会加载到缓存中。在即将发生大并发前手动触发加载缓存不同的key，设置不同的过期时间，让缓存失效的时间点尽量均匀。