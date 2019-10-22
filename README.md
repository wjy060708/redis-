# redis-面试笔记
## 1.redis是什么？
redis是一个基于内存的key-value数据库，类似于memcached，整个数据库在启动的时候统统将数据加载进内存进行操作，定期通过异步
的方式将数据flush进磁盘（即持久化）。因为其实纯内存操作，所以性能非常高，每秒可以处理10万次读写操作，是已知性能最好的key-value
数据库，Redis的出色之处不仅仅是性能，Redis最大的魅力是支持保存多种数据结构，此外单个value的最大限制是1GB，不像 
memcached只能保存1MB的数据，因此Redis可以用来实现很多有用的功能，
比方说用他的List来做FIFO双向链表，实现一个轻量级的高性 能消息队列服务，用他的Set可以做高性能的tag系统等等。另外Redis也可以对存入的Key-Value设置expire时间，
因此也可以被当作一 个功能加强版的memcached来用。 
Redis的主要缺点是数据库容量受到物理内存的限制，不能用作海量数据的高性能读写，因此Redis适合的场景主要局限在较小数据量的高性能操作和运算上。
## 2.redis相比于memcached？
### （1）.memcached支持的数据类型单一，只支持string类型的value，redis支持五中数据结构的value。
### （2）.memcached value值存储1MB数据 redis的value可存储1GB的数据。
### （3）.redis的速度由于memcched。
### （4）.redis可持久化数据（rdb,aof两种持久化方式）。
## 3.Redis支持哪几种数据类型？
String（字符串）、List（列表）、Set（集合）、Sorted Set（有序集合）、hashes（是一个键值(key=>value)对集合）
## 4.Redis主要消耗什么物理资源？
redis是一种基于内存高性能的数据库--- 主要依赖于内存。
## 5.Redis有哪几种数据淘汰策略即？
通过limits限制内存大小，缓存的过期策略**默认永不过期**
allkeys-lru: 尝试回收最少使用的键（LRU），使得新添加的数据有空间存放。
（1）.volatile-lru: 尝试回收最少使用的键（LRU），**但仅限于在过期集合的键,使得新添加的数据有空间存放**。<br>
（2）.allkeys-lru:最近最少使用，针对所有键。<br>
（3）.allkeys-random: 回收随机的键使得新添加的数据有空间存放。<br>
（4）.volatile-random: 回收随机的键使得新添加的数据有空间存放，**但仅限于在过期集合的键**。<br>
（5）.volatile-ttl: 回收在过期集合的键，并且优先回收存活时间（TTL）较短的键,使得新添加的数据有空间存放。<br>
（6）.noeviction: 不进行移除。
## 6.一个字符串类型的值能存储最大容量是多少？ 512M
512M
## 7.为什么Redis需要把所有数据放到内存中？
Redis为了达到最快的读写速度将数据都读到内存中，并通过异步的方式将数据写入磁盘。所以redis具有快速和数据持久化的特征。如果不将数据放在内存中，磁盘I/O速度为严重影响redis的性能。在内存越来越便宜的今天，redis将会越来越受欢迎。
 如果设置了最大使用的内存，则数据已有记录数达到内存限值后不能继续插入新值。
## 8.MySQL里有2000w数据，redis中只存20w的数据，如何保证redis中的数据都是热点数据？
 redis内存数据集大小上升到一定大小的时候，就会施行数据淘汰策略。
## 9.Redis有哪些适合的场景？
 （1）.会话缓存（Session Cache）最常用的一种使用Redis的情景是会话缓存（session cache）。用Redis缓存会话比其他存储（如Memcached）的优势在于：Redis提供持久化。当维护一个不是严格要求一致性的缓存时，如果用户的购物车信息全部丢失，大部分人都会不高兴的，现在，他们还会这样吗？幸运的是，随着 Redis 这些年的改进，很容易找到怎么恰当的使用Redis来缓存会话的文档。甚至广为人知的商业平台Magento也提供Redis的插件。<br>
 （2）.全页缓存（FPC）除基本的会话token之外，Redis还提供很简便的FPC平台。回到一致性问题，即使重启了Redis实例，因为有磁盘的持久化，用户也不会看到页面加载速度的下降，这是一个极大改进，类似PHP本地FPC。再次以Magento为例，Magento提供一个插件来使用Redis作为全页缓存后端。此外，对WordPress的用户来说，Pantheon有一个非常好的插件 wp-redis，这个插件能帮助你以最快速度加载你曾浏览过的页面。<br>
 （3）.队列Reids在内存存储引擎领域的一大优点是提供 list 和 set 操作，这使得Redis能作为一个很好的消息队列平台来使用。Redis作为队列使用的操作，就类似于本地程序语言（如Python）对 list 的 push/pop 操作。如果你快速的在Google中搜索“Redis queues”，你马上就能找到大量的开源项目，这些项目的目的就是利用Redis创建非常好的后端工具，以满足各种队列需求。例如，Celery有一个后台就是使用Redis作为broker，你可以从这里去查看。<br>
 （4）.排行榜/计数器Redis在内存中对数字进行递增或递减的操作实现的非常好。集合（Set）和有序集合（Sorted Set）也使得我们在执行这些操作的时候变的非常简单，Redis只是正好提供了这两种数据结构。所以，我们要从排序集合中获取到排名最靠前的10个用户–我们称之为“user_scores”，我们只需要像下面一样执行即可：当然，这是假定你是根据你用户的分数做递增的排序。如果你想返回用户及用户的分数，你需要这样执行：ZRANGE user_scores 0 10 WITHSCORESAgora Games就是一个很好的例子，用Ruby实现的，它的排行榜就是使用Redis来存储数据的，你可以在这里看到。<br>
 （5）.发布/订阅最后（但肯定不是最不重要的）是Redis的发布/订阅功能。发布/订阅的使用场景确实非常多。我已看见人们在社交网络连接中使用，还可作为基于发布/订阅的脚本触发器，甚至用Redis的发布/订阅功能来建立聊天系统！（不，这是真的，你可以去核实）。
## 10.Redis支持的Java客户端都有哪些？官方推荐用哪个？
 Redisson、Jedis、lettuce等等，官方推荐使用Redisson。
## 11.Redis和Redisson有什么关系？
**Redisson是一个高级的分布式协调Redis客服端**，能帮助用户在分布式环境中轻松实现一些Java的对象 (Bloom filter, BitSet, Set, SetMultimap, ScoredSortedSet, SortedSet, Map, ConcurrentMap, List, ListMultimap, Queue, BlockingQueue, Deque, BlockingDeque, Semaphore, Lock, ReadWriteLock, AtomicLong, CountDownLatch, Publish / Subscribe, HyperLogLog)。
## 12.Jedis与Redisson对比有什么优缺点？
Jedis是Redis的Java实现的客户端，其API提供了比较全面的Redis命令的支持；Redisson实现了分布式和可扩展的Java数据结构，和Jedis相比，功能较为简单，不支持字符串操作，不支持排序、事务、管道、分区等Redis特性。Redisson的宗旨是促进使用者对Redis的关注分离，从而让使用者能够将精力更集中地放在处理业务逻辑上。
## 13.Redis如何设置密码及验证密码？
config set requirepass “123456” 设置密码<br>
config get requirepass 获取密码<br>
auth “123456” 验证密码
## 14.redis的持久化
### 1.RDB方式
#### 1.1.概念：
在指定时间时间间隔内，将内存的数据flush到磁盘上，恢复是从磁盘上加载进内存中。
#### 1.2.原理：
redis会单独fork一个子进程来进行持久化，会将数据写入一个临时文件，当持久化完成再用临时文件替换上次持久化好的临时文件，整个过程中主进程并不进行I/O从操作，确保了性能。
#### 1.3.策略：
save 900 1 15分钟 有一次更改<br>
save 300 10 5分钟内有10次更改<br>
save 60 10000 60秒内有10000次更改
#### 1.4.优势：
适合大规模恢复，对数据的完整性和一致性要求不高（因为有可能会丢失最后一次更改的数据）
#### 1.5.劣势：
会丢失最后一次对数据的修改 fork进程时，内存的数据会被克隆一次，性能需要被考虑
### 2.aof方式
#### 2.1.概念：
以日志的形式记录每个写操作，将redis所有执行过的所有写指令记录下来，只许追加文件，不可修改文件，redis重启后会读取该文件重构数据
#### 2.2.配置
在配置文件中将appendOnly：yes 打开aof持久化方式
#### 2.3.策略
always：每次更新数据都会立即记录到磁盘中，性能差，但完整性好。<br>
everySec:没秒钟记录一次异步推荐，以秒为单位,如果宕机会有一秒的数据丢失<br>
no:不持久化
#### 2.4.aof文件重写
aof采用文件追加的方式进行数据持久化，导致文件越来越大，为避免此情况，新增重写机制，当aof文件的大小超过阈值时，redis会启动
aof文件内容压缩
#### 2.5.aof文件重写的触发机制：
redis会记录上次重写时aof文件的大小，默认当aof文件大小是上次rewrite后大小的一倍且文件大小超过64M时触发
#### 2.6.aof持久化的优势
比较灵活
#### 2.7.aof持久化的劣势
相同的数据集而言aof远大于rdb文件，恢复慢于rdb

















