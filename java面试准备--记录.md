#### 1、位运算

| 按位与                     | 按位或                         | 按位取反      | 按位异或                        |
| -------------------------- | ------------------------------ | ------------- | ------------------------------- |
| 0&0=0; 0&1=0; 1&0=0; 1&1=1 | 0\|0=0；0\|1=1；1\|0=1；1\|1=1 | ~1=0； ~0=1； | 0^0=0； 0^1=1； 1^0=1； 1^1=0； |
| 都为1才是1                 | 有1就是1                       |               | 相同为0，不同为1                |

#### 2、switch支持的类型

Incompatible types. Found: 'float', required: 'char, byte, short, int, Character, Byte, Short, Integer, String, or an enum

#### 3、用最有效率的方法算出2乘以8等於几?

2<< 3，(左移三位)因为将一个数左移n位，就相当于乘以了2的n次方，直接cpu计算，最快。

#### 4、使用final关键字修饰一个变量时，是指引用变量不能变，引用变量所指向的对象中的内容还是可以改变的。

#### 5、list遍历方式选择

实现了RandomAccess接口的list，优先选择for循环，其次foreach

未实现RandomAccess接口的list，优先选择iterator遍历，大size的数据不要使用for循环

#### 6、HashMap与HashTable的区别？

线程安全性？

效率？

null key？

初始容纳量大小不同？每次扩充大小不同？

​	hashtable初始11每次扩充为2n+1，hashmap初始16每次扩充为2n。

​	创建时给定初始值，hashtable使用该值，hashmap扩充至2的幂次方（**为了减少碰撞**）。

底层数据结构

​	当hashmap链表大于阈值（8），链表转化为红黑树

https://zhuanlan.zhihu.com/p/21673805

#### 7、hashmap死循环问题？（待看）

#### 8、哈希冲突？

即k1!=k2，但哈希函数计算出的哈希值相等calhash(k1) = calhash(k2)，导致哈希冲突。

#### 9、ConcurrentHashMap 和hashtable 区别？

线程安全方式：**jdk1.7时**，concurrenthashmap使用**分段锁**，每一把锁只锁容器其中一部分数据，多线程访问容器里的不同数据段的数据，不存在锁竞争，提高并发访问率。**jdk1.8后**，采用**CAS（乐观锁）和synchronized**来保证并发安全  ，**node数组+链表+红黑树**，synchronized只锁定当前链表或红黑二叉树的首节点，这样只要hash不冲突，就不会产生并发  。

hashtable使用**同一把锁**，例如，使用put 添加元素，另一个线程不能使用 put 添加元素，也不能使用 get，竞争会越来越激烈效率越低。

#### 10、synchronized

加到非static方法上是给对象实例加锁

static方法：类锁住

代码块synchronized（this）：对象锁住

代码块synchronized（class）：类锁住

#### 11、 指令重排问题

> 需要注意 uniqueInstance 采用 volatile 关键字修饰也是很有必要。
> uniqueInstance 采用 volatile 关键字修饰也是很有必要的， `uniqueInstance = new Singleton()`; 这段代码其实是分为三步执行
>
> 为 uniqueInstance 分配内存空间、初始化 uniqueInstance、将 uniqueInstance 指向分配的内存地址
> 但是由于 JVM 具有指令重排的特性，执行顺序有可能变成 1->3->2。指令重排在单线程环境下不会出先问题，但是在多线程环境下会导致一个线程获得还没有初始化的实例。例如，线程 T1 执行了 1 和 3，此时 T2 调用
> getUniqueInstance() 后发现 uniqueInstance 不为空，因此返回 uniqueInstance，但此时 uniqueInstance 还未被初始化。
> 使用 volatile 可以禁止 JVM 的指令重排，保证在多线程环境下也能正常运行。  

#### 12、synchronized和ReenTrantLock区别

都是可重入锁

synchronized依赖于jvm，ReenTrantLock依赖于API（需要使用lock(),unlock(),tryfinally）

ReenTrantLock可指定是公平锁还是非公平锁（构造函数可配置）

#### 13、volatile  

禁止jvm指令重排

#### 14、synchronized、volatile区别

volatile只能作用于变量

volatile主要用于解决变量在多个线程之间的可见性，synchronized解决多个线程之间访问资源的同步性

#### 15、为什么用线程池？

降低资源消耗：重复利用已创建的线程降低创建销毁造成的消耗

提高响应速度：取出即用，不需创建

提高线程的可管理性：利于调优

#### 16、Runnable Callable区别

Runnable的run()无返回值，Callable的run()有返回值

#### 17、了解Atomic原子类吗

利用CAS（compare and swap） + volatile和native方法来保证院子操作，避免synchronized的高开销。

#### 18、了解AQS吗

ReenTrantLock就是基于AQS的，按我自己的话简单讲讲就是，假如A资源被占用了，锁住A，后续请求访问A的线程均放到队列（虚拟的双向队列，节点之间才互相关联）中去。而这个队列

AQS使用一个volatile的int共享变量，保证可见。

AQS两种资源共享方式：

- 独占（ReenTrantLock）
  - 公平锁：按顺序来
  - 非公平锁：谁抢到是谁的
- 共享（CountDownLatCh ReadWriteLock  等）

#### 19、设计模式

需要写一下单例、工厂、代理、观察者

#### 20、四次挥手怎么解释？

比较通俗的类比：

> A 和 B 打电话，通话即将结束后，A 说“我没啥要说的了”，B回答“我知道了”，但是 B 可能还会有要说的
> 话，A 不能要求 B 跟着自己的节奏结束通话，于是 B 可能又巴拉巴拉说了一通，最后 B 说“我说完了”，A 回答“知道了”，这样通话才算结束。

#### 21、linux用过哪些指令？

启动服务 systemctl service start

查看cpu等信息：top

目录创建：mkdir [filename]

显示当前目录所有目录和文件：ls

移动：mv [from] [to]

复制：cp -r [from] [to]  （-r代表递归拷贝）

文件创建：touch

文件查看：cat

动态监控文件的变化：tail -f （在日志文件监控时用过）

修改文件：vim

解压： tar （可带参数：z使用gzip，c压缩/x解压，v显示过程，f指定文件名）  第二个参数可带-C指定解压位置

显示当前路径：pwd

当前进程：ps -ef

网络：ifconfig

端口使用：netstat

#### 22、mysql有哪些存储引擎？有什么区别？

Myisam（米儿sam），innodb

事务上：myisam不支持，innodb支持

锁：myisam默认表锁，不支持行级锁；innodb默认行锁，可配置表级锁

外键：myisam不支持，innodb支持

侧重点：myisam侧重性能，每次查询具有原子性，速度比innodb快

选择：myisam更适合读密集，innodb适合写密集，至于实际选择，还得看实际的业务反应到数据操作上是怎样的。

#### 23.主键自增还是uuid？

单机数量大--自增，不多--uuid

分布式数量大（比单机意义上的大要大很多，可能是千万级别）--自增，不多--uuid

#### 24.覆盖索引怎么理解？

简单说就是  该索引涉及的列 包含 select 的所有列

innodb中除主键索引，叶子结点存储的是**主键+列值**

假如不满足，那么会回表查询，如index(name)，对于`select id,name,sex from table` 而言，name能在索引数中找得到，但sex不行，这是就会回表再查找一次，导致查询慢。

#### 25.为什么索引能提高访问速度？

底层可以根据索引列名进行二分查找，复杂度约等于 log2N

#### 26.什么是最左前缀原则？

索引建立后，做条件查询时，查询条件要从左到右按顺序匹配到索引的某一列或多列，才有命中索引。

但假如条件列与索引列完全一致，则依然可以命中，忽略条件查询的顺序。

#### 27.说一下mysql分库分表？

- 垂直分表：列的拆分。通俗讲也就是纵向切开，左边一部分列，右边一部分列，两边的数据要有各自的特征。
  - 垂直拆分使得单行数据变少，减少I/O，易于维护。
  - 但主键会冗余。会引起join操作，但join可以在java代码处理。
- 水平分表：行的拆分。就是说采取某种规则策略，将数据表横向切开。表数据达到百万级别，可以考虑。
  - 水平拆分表对于单机来讲，性能提升不大，最好是**水平分库**，但分片事务难解决，跨界点Join性能较差，逻辑复杂 。
  - 客户端代理和中间件代理？

#### 28.为什么用redis？为什么是redis？

提升并发访问性能。

相对于memcached，支持更多的数据类型。

redis可以将数据持久化，就是说可以把内存中的数据存到磁盘中，重启时可读取磁盘的数据。memcached不支持。

#### 29.redis数据类型有哪些？分别在哪些场景下使用比较合适？

String，Hash，List（双向链表），Set，Sorted Set

#### 30.redis常用指令？

set、get、hget、hset、lpush、rpush、lpop、rpop、lrange（可用于分页）

#### 31.如何设计redis的过期策略？

redis内存淘汰机制

针对已设置过期时间的数据集有几个策略：

volatile-LRU：最近最少使用原则

volatile-TTL：即将要过期的

volatile-Random：任意选择淘汰

……

#### 32.redis如何做持久化？

RDB（快照）：默认的持久化方式，复制快照的其他服务器

AOF（追加文件）：开启方式 `appendonly yes`

可设置的追加策略有三种：always每次写操作、everysec每秒钟、no让操作系统决定。一般选择**everysec**

#### 33.说一下缓存雪崩

缓存大面积失效，导致瞬间有大量请求打到数据库上，数据库承受不住崩掉了

处理方法：

事前：尽量保证缓存的高可用，集群啊，内存淘汰机制啊之类的

事中：也就是当缓存大面积失效时，打到请求量会激增，故要做好降级措施，比如有百分之40的请求会通过走向数据库，另外的百分之60的请求被降级，返回默认数据（提示语，空数据啥的）。

事后：备份恢复，尽快根据redis的持久化数据恢复。

#### 34.了解过分布式锁吗？

针对分布式场景下，对同一数据的写操作可能会导致数据错误，这时候可以使用分布式锁，对该数据加上锁（比较典型的是某件商品的库存）

redis与zookeeper

#### 35.怎么保证缓存与数据库的双写一致性？

cache aside pattern

读：读缓存，没有则从数据库找出，写入缓存

写：更新数据库后，删除缓存

场景1：先update 数据库，再删缓存，如果删缓存失败了，数据不一致

解决：先删缓存再删数据库

场景2：



如果微服务网关每秒10万请求，该怎么优化？