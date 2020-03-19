# Redis

## NoSQL的概述

NoSQL（Not Only SQL）意为“不仅仅是SQL”泛指非关系型的数据库

**这些类型的数据存储不需要固定的模式，无需多余操作就可以横向扩展。**

1. 易扩展
2. 大数据量高性能
3. 多样灵活的数据模型
4. 传统RDBMS VS NOSQL

### NoSQL数据库的四大分类

1. KV键值：redis
2. 文档型数据库：Mongodb
3. 列存储数据库：HBase
4. 图关系数据库：infoGrid

### 在分布式数据库中CAP原理CAP+BASE

#### CAP：

1. C：Consistency（强一致性）
2. A：Availability（可用性）
3. P：Partion tolerance（分区容错性）

CAP的理论就是说在分布式存储系统中，最多只能实现上面的两点。

而由于当前的网络硬件肯定会出现延迟丢包等问题，所以

**分区容忍性是我们必须要实现的**

。

所以我们只能在

**一致性**和**可用性**

之间进行权衡，没有NoSQL系统能同时保证这一点。

**C：强一致性  A：高可用性   P：分布式容错性**

**CA 传统Oracle数据库**

**AP大多数网站架构的选择**

**CP Redis、Mongodb**

#### **BASE**

就是为了解决关系数据库强一致性引起的问题而引起的可用性降低而提出的解决方案。

BASE其实是下面三个术语的缩写：

**基本可用（Basically Available）**

**软状态（Soft state）**

**最终一致（Eventually consistent）**

它的思想是通过让系统放松对某一时刻数据**一致性**

的要求来换取系统整体伸缩性和性能上改观。缘由就在于大型系统往往由于地域分布和极高性能的要求，不可能采用分布式事务来完成这些指标，我们必须采用另外一种方式来完成，这里BASE就是解决这个问题的办法。

#### 分布式和集群

分布式：不同的多台服务器上面部署不同的服务模块（工程），他们之间通过Rpc/Rmi之间通信和调用，对外提供服务和组内协作。

集群：不同的多台服务器上面部署相同的服务模块，通过分布式调度软件进行统一的调度，对外提供服务和访问。

## redis

Redis：REmote DIctionary Server（远程字典服务器）

redis是一个高性能的（key/value）分布式内存数据库，基于内存运行并支持持久化的NoSQL数据库，被称为数据结构数据库。

### Redis与其他key-value缓存产品有一下三个特点

1. Redis支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用
2. Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储
3. Redis支持数据的备份，即master-slave模式的数据备份。

### Redis启动后杂项基础知识讲解

1. 默认16个数据库，类似数组下表从零开始，初始默认使用零号库
2. Select命令切换数据库
3. Dbsize查看当前数据库的key的数量
4. Flushdb：清空当前库
5. Flushall：通杀全部库
6. 同一密码管理，16个库都是同样密码，要么都ok要么一个也连不上
7. Redis索引都是从零开始
8. 为什么默认端口是6379 

### Redis的五大数据类型

1. String（字符串）

   string是redis最基本的类型，可以理解为Memcached一样的类型，一个key对应一个value。

   string类型是二进制安全的，意思是redis的string可以包含任何数据。比如ipg图片或者序列化的对象。

   string类型是Redis最基本的数据结构，一个redis中字符串value最多可以是512M。

   

2. Hash（哈希，类似java里的Map）

   Redis hash是一个键值对集合。

   Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象。

   类似java里面的Map<String,Object>

   

3. List（列表）

   Redis列表是简单的字符串列表，按照插入顺序排序。可以添加一个元素到列表的头部（左边）或者尾部（右边）。

   它的底层是一个链表。

   

4. Set（集合）

   Redis的Set是string类型的无序集合。它是通过HashTable实现的。

   

5. Zset（sorted set：有序集合）

   Redis zset和set一样也是string类型元素的集合，且不允许重复的成员。

   **不同的是每个元素都会关联一个double类型的分数**

   。

   redis正是通过分数来为集合中的成员进行从小到大排序。zset的成员是唯一的，但分数（score）却可以重复。

### 常用命令：

#### key：

1. keys *
2. exists key的名字,判断某个key是否存在（1就是有，0没有）
3. move key db -->当前库就没有了，被移出了
4. expire key 秒钟：为给定的key设置过期时间
5. ttl（time to leave） key查看还有多少秒过期，-1表示永不过期，-2表示已过期（永久删除）
6. type key 查看你的key是什么类型
7. del key 删除

#### String：单值单value

1. set/get/del/append/strlen

2. Incr/decr/incrby/decrby,一定要是数字才能进行加减

3. getrange/setrange

   getrange：获取指定区间范围内的值，类似between....and的关系。

   从0到-1表示全部

   setrange设置指定区间范围内的值，格式是setrange key值，具体值。

   

4. setex(set with expire)键秒值/setnx(set if not exist)

5. mset/mget/msetnx（增加多个）

6. getset(先get在set)

 

#### List：单值多value

1. lpush/rpush：添加
2. lrange :查看
3. lpop/rpop:出元素
4. lindex,按照索引下表获得元素（从上到下）
5. llen:查看长度
6. lrem  key: 删N个value
7. ltrim key 开始index 结束index:截取指定范围的值后再赋值给key
8. rpoplpush 源列表 目的列表:剪切到目的列表
9. lset key index value：修改指定index位置的值
10. linsert key before/after 值1 值2：在前一个值1插入值2
    性能总结：前后插入快，中间插入难





#### Set：单值多value

1. sadd：添加
2. smembers：查看集合元素
3. sismember：判断某元素是否存在
4. scard：获取集合里面的元素个数
5. srem key value：删除集合中元素
6. srandmember key 某个整数：随机出几个数
7. spop key：随机出栈
8. smove key2 key2 在key1里某个值    作用是将key1里的某个值赋给key2
9. 数学集合类
   	差集：sdiff
      	交集：sinter
      	并集：sunion



#### Hash：KV模式不变，但V是一个键值对

1. hset：如：hset user name z3

   添加name z3 到user

2. hget：hset user name：获得name 的值

3. hmset：添加多个

4. hgetall：获得所有value（键值）

5. hdel：删除

6. hlen：长度

7. hexists key ：在key里面的某个值的key是否存在

8. hkey/hvals：查看所有key和value

9. hincrby/hincrbyfloat：增加数字

10. hsetnx：判断是否存在之后添加

#### Redis有序集合Zset（sorted set）

和set的区别：在set基础上加上一个score值，之前set是k1 v1 v2 v3,现在zset是k1 score v1 score v2

1. zadd/arange
2. zrangebyscore key 开始score 结束score
   	withscores
      	（  不包含
      	limit作用是返回限制  limit开始下标步  多少步
3. zrem key 某score下对应的value值，作用是删除元素
4. zcard/zcount key score区间/zrank key values值，作用是获得下标值/zscore key 对应值，获得分数
5. zrevrank key values值，作用是逆序获得下标值
6. zrevrange
7. zrevrangebyscore key 结束score 开始score





## 解析配置文件redis.conf

#### general通用：

1. Daemonize：守护线程启动

2. Pidfile：进程管道文件

   /var/run/redis.conf

3. Port：6397

4. Tcp-backlog：

   tcp-backing 551

   设置tcp的backlog，backlog其实是一个连接队列，backlog队列总和=未完成三次握手队列+已经完成三次握手队列。

   在高并发环境下你需要一个高backlog值来避免慢客户端连接问题。注意linux内核会将这个值减小到/proc/sys/net/core/somaxconn的值，所以需要确认增大somaxconn和tcp_max_syn_backing两个值来达到想要的效果。

5. Timeout：0 不关闭，超时连接，过多久断开连接

6. Bind

7. Tcp-keepalive：单位为秒，如果设置为0，则不会进行Keepalive检测，建议设置成60

   心跳机制！！！

8. Loglevel：

   debug（开发阶段）

   verbose

   notice

   warning 生产模式

   级别越高，日志越少

9. Logfile：默认日志文件夹名

10. Syslog-enabled：是否把日志输出到syslog中

11. Syslog-ident：指定syslog里的日志标志

12. Syslog-facility：指定syslog设备，值可以是user或local0-local7

    默认0

13. Databases：安装完redis默认为多少个库

#### SNAPSHOTTING快照

1. ##### Save

   1. ​	**save <秒钟>  <写操作次数>**

   ​	RDB是整个内存压缩过的Snapshot，RDB的数据结构，可以配置复合的快照触发条件

   默认

   **1分钟内改变了1万次，**

   **或5分钟内改了10次，**

   **或15分钟内改了1次。**

   

   **禁用方法**

   如果想禁用RDB持久化策略，只要不设置任何save指令，或者给save传入一个空字符串参数也可以。比如：**save “”**

   

2. ##### Stop-writes-on-bgsave-error：出错就停止写

   默认是yes，如果配置成no，表示不在乎数据不一致或者有其他的手段发现和控制。

3. ##### rdbcompression：是否压缩快照

   默认是yes

   rdbcompression：对于存储到磁盘中的快照，可以设置是否进行压缩存储。如果是的话，redis会采用LZF算法进行压缩。如果不想消耗CPU来进行压缩的话，可以设置为no关闭此功能。

4. ##### rdbchecksum：是否验证快照压缩

   默认是yes

   rdbchecksum：在存储快照后，还可以让redis使用CRC64算法来进行数据校验，但是这样做会增大大约10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能。

5. ##### dfgilename

6. ##### dir

#### REPLICATION复制

#### SECURITY安全

config get requirepass

config set requirepass"123456"//设置密码

auth 123456//输入密码  

#### LIMITS限制

Maxclients：最多连接人数

Maxmemory：

**Maxmemory-policy**：最大内存的缓存过期清除策略（默认永不过期）

1. **vlatile-lru**:使用LRU算法移除key，只对设置了时间过期时间的键
2. **allkeys-lru**：使用LRU算法移除key
3. **volatile-random**：在过期集合中移除随机的key，只对设置了过期时间的键
4. **allkeys-random**：移除随机的key
5. **volatile-ttl**：移除那些TTL值最小的key，即那些最近要过期的key
6. **noeviction**：不进行移除，针对写操作，只是 返回错误信息

Maxmemory-samples：选取样本 默认5个

#### APPEND ONLY MODE追加

1. appendonly：默认为no，yes开启aof持久化
2. appendfilename：默认aof恢复文件名
3. Appendfsync
   	Always：同步持久化，每次发生数据变更会立即记录到磁盘，性能较差但数据完整性比较好
      	Everysec：**出厂默认推荐，**异步操作，每秒记录，如果一秒内宕机，有数据丢失
      	No
4. No-appendfsync-on-rewrite：重写时是否可以运行Appendfsync，用默认no即可，保证数据安全性。
5. Auto-aof-rewrite-min-size：设置重写的基准值
6. Auto-aof-rewrite-perentage：设置重写的基准值

#### 常见配置redis.conf介绍









## Redis的持久化

dump和aof文件能同时存在，先加载的是aof

### RDB

在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是Snapshot快照，它恢复时将快照文件直接读到内存里。

#### 过程

Redis会单独创建（Fork）一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。    

整个过程中，主进程是不进行任何IO操作的，这就确保了极高的性能 

如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加的高效。RDB的缺点是最后一次持久化后的数据可能丢失。

#### Fork

Fork的作用是复制一个与当前进程一样的进程。新进程的所有数据（变量、环境变量‘程序计数器等）数值都和原进程一致，但是是一个全新的进程，并作为原进程的子进程。

#### 文件形式

RDB保存的是dump.rdb文件

#### 配置位置

**SnapShotting快照**

#### 如何触发RDB快照

1. 配置文件中默认的快照配置
   	冷拷贝后重新使用
      		可以cp dump.rdb dump_new.rdb
2. 命令save或者是bgsave
   	Save：save时只管保存，其他不管，全部zuse
      	**BGSAVE：Redis会在后台异步进行快照操作，快照操作同时还可以响应客户端请求。可以通过lastsave命令获取最后一次成功执行快照的时间。**
3. 执行flushall命令，也会产生dump.rdb文件，但里面是空的，无意义。

#### 如何恢复

将备份文件（dump.rdb）移动到redis安装目录并启动服务即可
CONFIG GET dir获取目录

#### 优势

适合大规模的数据恢复
对数据完整性和一致性要求不高

#### 劣势

在一定间隔时间做一次备份，所以如果redis意外down掉的话，就会丢失最后一次快照后的所有修改。
Fork的时候，内存中的数据被克隆了一份，大约2倍的膨胀性需要考虑。

#### 如何修改

动态所有停止RDB保存规则的方法：redis-cli config set save ""

### AOF

**以日志的形式来记录每个写操作**，将Redis执行过的所有写指令记录下来（读操作不记录），只许追加文件但不可以改写文件，redis启动之初会读取该文件重新构建数据，换言之，redis重启的话根据日志文件的内容将写指令从前到后执行一次已完成数据的恢复工作。

#### AOF启动/修复/恢复

1. ​	正常恢复
   ​		启动：设置Yes
   ​		将有数据的aof文件复制一份保存到对应目录（config get dir）
   ​		恢复：重启redis然后重新加载
   ​	
2. 异常恢复
   		启动：设置Yes
      		备份被写坏的AOF文件
      		修复：
      			Redis-check-aof   --fix进行修复
      		恢复：
      			重启redis然后重新加载

#### Rewrite

##### 	是什么

​		AOF采用文件追加方式，文件会越来越大，为避免出现此种情况，新增了重写机制，当AOF文件的大小超过所设定的阈值时，Redis就会启动AOF文件的内容压缩，只保留可以恢复数据的最小指令集。**可以使用命令gbrewriteaof**

##### 	重写原理

​		AOF文件持续增长而过大时，会**fork出一条新进程来将文件重写（也是先写临时文件最后在rename），遍历新进程的内存中数据，每条记录有一条的Set语句**。重写aof文件的操作，并没有读取旧的aof文件，而是将整个内存中的数据库内容用命令的方式重写了一个新的aof文件，这点和快照有点类似。

##### 	触发机制

​		Redis会记录上次重写时的AOF大小，默认配置是当AOF文件大小是上次rewrite后大小的**一倍且文件大于64M时**触发。

#### 优势

​	每秒同步：appendfsync always  同步持久化  每次发生数据变更会被立即记录到磁盘  性能较差但数据完整性比较好
​	每修改同步：appendfsync everysec  异步操作 ，每秒记录  如果一秒内宕机，有数据丢失。
​	不同步：appendfsync no  从不同步

#### 劣势

​	相同数据集的数据而言aof文件要远大于rdb文件，恢复速度慢于rdb
​	AOF运行效率要慢于rdb，每秒同步策略效率较好，不同步效率和rdb相同。





### Redis的事务

可以一次执行多个命令，本质是一组命令的集合。一个事务中的所有命令都会序列化，按顺序地串行化执行而不会被其他命令插入，不许加塞。

一个队列中，一次性、顺序性、排他性地执行一系列命令

#### 常用命令：

DISCARD：取消事务，放弃执行事务块内的所有命令。

EXEC：执行所有事务块的命令。

MULTI：标记一个事务块的开始。

UNWATCH：取消WATCH命令对多有key的监视。

WATCH key [key......]：监视一个（或多个）key，如果在事务执行之前这个key被其他命令所改动，那么事务将打断。

#### 开启事务步骤：

1. 开启：以MULTI开始一个事务
2. 入队：将多个命令入队到事务中，接到这些命令并不会立即执行，而是放到等待执行的事务队列里面。
3. 执行：由EXEC命令触发事务

#### ps：

##### 悲观锁：

顾名思义，每次去拿数据的时候都被认为别人会修改，所以每次在拿数据的时候都会被锁上，这样别人想拿这个数据就会block直到它拿到锁，传统的关系型数据库里边就用到了很多这种锁机制，比如行锁、表锁等，读锁、写锁等，都是在做操作之前先锁上。

##### 乐观锁：

每次去拿数据的时候都认为别人不会修改，**所以不会上锁**，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用**版本号等机制**。乐观锁适用于多度的应用类型，这样可以**提高吞吐量**。锁策略：提交版本必须大于记录当前版本才能执行更新。



#### 常见情况：

Case1：正常放行

Case2：放弃事务

Case3：全体连坐

Case4：冤头债主

Case5：watch监控

1. Watch指令，类似乐观锁，事务提交时，如果Key的值已经被别的客户端改变，比如某个list已经被别的客户端push/pop过了，整个事务队列都不会被执行。
2. 通过WATCH命令在事务执行之前监控了多个keys，倘若在WATCH之后有任何key的值的变化，EXEC命令执行的事务都将被放弃，同时返回Nullmulti-bulk应答以通知调用者事务执行失败。

#### 特性：

1. 单独的隔离操作：事务中的所有命令都会被序列化、按顺序地执行。事务在执行的构成中，不会被其他客户端发送来的命令请求所打断。
2. 没有隔离级别的概念：队列中的命令没有提交之前都不会实际的被执行，因为事务提交前任何指令都不会被实际执行，也就不存在“事务内的查询要看到事务里的更新，在事务外查询不能看到”这个让人万分头痛的问题。
3. 不保证原子性：redis同一个事务中如果有一条命令执行失败，其后的命令仍然会被执行，没有回滚。

## Redis的复制（Master/Slave）

### 定义：

就是我们所说的主从复制，主机数据更新后根据配置和策略，自动同步到备机的master/slver机制，Master以写为主，Slave以读为主。

### 作用：

1. 读写分离
2. 容灾恢复

### 常用命令：

#### slaveof 主库IP 主库端口：

从库连接主库

#### Info replication：

查看本库数据

#### SLAVEOF no one：

使当前数据库通知与其他数据库的同步，转成主数据库。

### 注意：

1. **配从（库）不配主（库）**
2. **从库配置：slaveof主库IP主库端口**
3. **一主二仆**

### 从库配置文件修改

1. 拷贝多个redis.conf文件
2. 开启daemonize yes
3. Pid文件名字
4. 指定端口
5. Log文件名字
6. Dump.rdb名字

### 一主二仆：

#### 如果从库连接主库，会把之前的数据读进来吗

会的

#### 如果主机和从机一起写一个值，从机能覆盖主机的值吗

不行，主从复制读写分离的原则，主机才能写，从机不行

#### 主机挂了，从机会怎么样，主机回来了呢

从机原地待命，身份没变，主机回来了，关系照旧

#### 从机挂了呢，需要重新连接主机吗

需要，除非从机配置文件有配置



### 薪火相传

上一个Slave可以是下一个slave的Master，slave同样可以接受其他slaves的连接和同步请求，那么该slave作为链条中下一个的master，可以有效减轻master的写压力。

### 反从为主

SLAVEOF no one：使当前数据库通知与其他数据库的同步，转成主数据库。

原来的master回来了，没人连接



### 主从复制的原理

1. Slave启动成功连接到master后会发送一个sync命令
2. Master接到命令启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令，在后台进程执行完毕之后，master将传送整个数据文件到slave，以完成一次完全同步。
3. 全量控制：而slave服务在接收到数据库文件数据后，将其存盘并加载到内存中。
4. 增量控制：master继续将新的所有收集到的修改命令一次传给slave，完成同步
5. 但是只要是重新连接master，一次完全同步（全量复制）将被自动执行。

### 哨兵模式

反客为主的自动版，能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库

#### 步骤：

1. 调整结构，6379带着80、81
2. 自定义的/myredis目录下新建sentinel.conf文件，名字决不能错。
3. 配置哨兵。填写内容
   1. ​	sentinel monitor被监控数据库名字（自己起名字）127.0.0.1 6379 1
   2. ​	上面最后一个数字1，表示主机挂掉后slave投票看让谁解题成为主机，得票数多的成为主机
4. 正常主从演示
   原有的master挂了
5. 重新主从继续开工，info replication查查看

#### 问题：如果之前的master重启回来，会不会双master冲突？

不会冲突，之前的master回来之后，哨兵监测到，**之前的master会编程slave**





### 缺点：

复制延迟：由于所有的写操作都是先在Master上操作，然后同步更新到slave上，所以从master同步到slave机器有一定的延迟，当系统很繁忙的时候，延迟问题会更加严重，slave机器数量的增加也会使这个问题更加严重。

