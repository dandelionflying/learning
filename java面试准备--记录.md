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

> volatile本质是在告诉jvm当前变量在寄存器（工作内存）中的值是不确定的，需要从**主存**中读取； synchronized则是锁定当前变量，只有当前线程可以访问该变量，其他线程被阻塞住。

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

**场景1：先update 数据库，再删缓存，如果删缓存失败了，数据不一致**

解决：先删缓存再删数据库

**场景2：先删缓存，后修改数据库，此时还没修改，第二个请求过来发现缓存没有，则去数据库拿数据并写到缓存，这时数据库还是旧数据，随后上一个请求走完了数据库更新完了，一样会出现数据不一致。**

解决：**数据库与缓存的更新与读取操作进行异步串行化**

更新数据时，根据数据的唯一标识（如商品的id），及一定的策略，将操作放到一个jvm内部的队列中；

读取时，如果发现缓存为空，则到数据库读取新数据并更新缓存，那么这一步也要根据唯一标识去路由到同一个队列中，排在上一个操作的后面，也就是所谓的“串行化”。

这里有一个优化点：假如这个队列中某一个写请求后，接连跟着十几个读操作，那么完全没有必要都放到队列里，后续的读直接等待，直到缓存里有值了直接取（也就是说前面的读操作进行了读数据库+缓存更新的操作），当放到队列里会不断执行读数据库+缓存更新的操作，完全一样的数据没有意义。

至于等待多久，根据实际项目要求的性能，设定等待时间。

**场景3：在场景2的基础上，如果部署了多台机器，那么同一个数据的读/写操作可能到了不同的机器上，数据也会不一致**

解决：自己写路由策略或者利用nginx，将同一数据（可以通过参数，比如请求中商品的id）的请求路由到同一台机器上。

**场景4：热点数据导致请求打到同一机器上，导致该机器压力过大**

其实到这个地步的话，个人觉得直接熔断算了，还是要根据实际情况，做各种压测，看看需不需要考虑进去。

#### 36.如果微服务网关每秒10万请求，该怎么优化？

1.看机器配置，比如部署在8核16g上，对网关路由转发的请求，每秒可以1000+，10台机器就是1w+

实际每秒10w不太多，实际情况要去做压测。



#### 37.说下spring

spring的出现主要是提高开发效率，提升系统的可维护性，她提供了一些模块和方案，像他的核心容器、web的像springmvc，aop，依赖注入，包括数据库的jdbc等等。

#### 38.谈谈对于Spring IOC和AOP的理解？

ioc称作控制反转，是一种设计思想，简单讲就是我们不用频繁的自己去创建对象，去考虑何时创建何时销毁，交给spring容器来控制，spring中通过xml来配置，springboot中则是以注解的方式来配置，简化开发。

IOC的初始化过程为：读取--解析--注册

AOP是面向切面变成，我们一般将一些通用的、与业务无关但又贯穿多个业务流程的逻辑抽离出来，做切面处理，比如权限、日志。AOP基于动态代理。

#### 39.SPring中单例bean的会有线程安全问题吗？

会有，但我们常用的 `Controller`、`Service`、`Dao` 这些 Bean 是无状态的。无状态的 Bean 不能保存数据，因此是线程安全的。

#### 40.@Component和@Bean的区别？

前者作用域类，后者作用于方法。

Component是根据类类型去装配注册，后者是根据方法中定义的实例，把实例丢到了bean容器中。

#### 41.bean的生命周期是怎样的？

传统的spring来讲，

找到bean的定义-->解析-->执行bean构造器-->注入属性

-->调用init-method指定的初始化方法-->使用-->使用结束销毁

#### 42.了解SpringMVC吗？

M：model，对应到项目中的service、dao类和实体类这些

V：view，视图层，页面渲染

C：控制层，对应到controller

#### 43.SpringMVC工作原理（请求流程）？

前端请求过来后，先到dispatcherServlet

-->dispatcherServlet解析对应的handler （使用HandlerMapping）

-->解析后（解析的结果就是找到对应的controller），交给HandlerAdapter适配器处理

-->HandlerAdapter根据解析的结果调用controller处理

-->处理后得到一个ModelAndView

-->由ViewResolver解析ModelAndView找到对应的View视图（页面）

-->由DispatcherServlet解析得到Model交给View视图

-->View返回给客户端

#### 44.讲下mybatis缓存？

分一级缓存和二级缓存，默认一级缓存（放在session中），二级缓存需要另外配置开启，

#### 45.mybatis中#{}和${}区别

#{}可以防注入，预处理阶段会解析成？的形式，而${}会直接将值附上去。

#### 46.mybatis怎么实现延迟加载？

https://blog.csdn.net/eson_15/article/details/51668523?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-2.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-2.control

开启配置项，定义resultMap其中使用association标签定义延迟加载的内容

原理：基于CGLIB的动态代理，以前看过详细流程记不清了

#### 47.mybatis有啥好处？

对于个人使用的感受就是，更灵活，sql掌控在自己手里，自己去编写sql，从ava代码抽离，不像jpa那样需要操作java对象，其实两者的选择看系统架构吧，以及是否有大量的复杂sql。

mybatis通过底层的映射自动转换成javabean 也不需要自己去编写转换

#### 48.mybatis接口绑定？

已经是开发必备的了，就是xml中配置对应到java接口的方法名，使接口映射到指定的mapper中去

#### 49.Mybatis的XML文件中，不同的XML映射文件，id可否重复？

看有没有配置命名空间了

#### 50.mybatis批处理

使用BatchExecutor

#### 51.mapper中如何传递多个参数？

方法参数前使用 @Param注解，xml中就可以直接用#{name}了。

#### 52.mybatis比ibatis改进在哪？

有接口绑定

#### 53.mybatis和ibatis的核心处理类是啥？

mybatis：sqlSession，Ibatis：记不清叫啥了

#### 54.事务的特性

ACID：原子性、一致性、隔离性、持久性

#### 55.事务隔离级别

读未提交：最低的隔离级别，会产生很多问题

读已提交：解决脏读

可重复读：解决脏读、不可重复读

可串行化：最高级别，遵循ACID，解决所有，但效率很低

#### 56.当要列举spring的功能的时候，可以列几个：

IOC、AOP、容器、事务管理

#### 57.切面有几种通知类型？

before、after Returning、after Throwing、After（finally）、Around

#### 58.静态代理与动态代理

代理模式的基本流程是，A类，自定义Proxy类都实现同一接口，代理类包含接口类型的成员变量，通过setter或构造器传入实现类。代理模式可以让你能将一些额外的操作抽离出来，而不是放在实际业务中，做到对这些额外的操作的修改不会对业务造成影响。比如你想跟踪实际业务方法的调用，可以写一个代理类去测试，而不是将测试需要额外编写的代码加到业务代码中去。

静态代理，编译时已确定代理关系。

动态代理，运行时通过反射+代理。

动态代理创建方式：`Proxy.newProxyInstance()`，参数包含一个从被加载的对象获取的类加载器`.getClass().getClassLoader()`、该类的接口列表`.getClass().getInterfaces()`以及`InvocationHandler`的一个实现。

> 动态代理可以将所有调用重定向到调用处理器，通常会向调用处理器的构造器传递给一个实际对象的引用。从而使得调用处理器在执行其中介任务时，可以将请求转发。

spring里也用了很多动态代理，比方说请求的处理，像feign，就是底层会针对接口生成动态代理，去做调用。还有mybatis的一些插件也用到了动态代理。

#### 59.mysql 锁的优化策略



#### 60.eureka底层原理

eureka中有一个服务注册表，利用了两级缓存（优化并发冲突）来维护，ReadWriteCache实时同步注册表的变动，ReadOnlyCache以一定的策略去从ReadWriteCache同步新的数据，而服务发现则是从ReadOnlyCache中去获取注册表数据。所谓的“策略”就是会有线程去做定时同步，定时时长可配置。

#### 61.feign是怎么个流程？

feign底层大致流程是，对接口使用了feign注解之后，他会针对这个注解标注的接口生成动态代理，然后我们针对feign的动态代理去调用他的方法的时候，底层会生成一个http协议的请求。底层使用http通信组件httpclient发送请求，发送之前，先通过ribbon去从本地的eureka注册表的缓存里获取出对方机器的列表，进行负载均衡选出一台机器来，对这台机器发送http请求。

#### 62.eureka服务注册发现慢怎么优化？

可以进行配置，两级缓存中，定是同步的时间间隔，服务注册的心跳间隔，服务发现的间隔等。参数记不清了。

#### 63.微服务网关的动态路由

通过可视化的界面把路径与服务的对应写到数据库库（db，redis，mongodb，甚至是文件）中，网关微服务定时去获取最新的路由信息，放到网关的路由表中去。

#### 64.灰度发布

通过数据库配置url是否启用灰度发布，写一个请求的过滤器，发现灰度开启了，就依据特定的逻辑去决定是否要请请求转发到新版本的服务上去。这里的逻辑可能是特定的用户群、特定的角色或是随机抽样一定的比例等等。

#### 65.一个服务开发到上线经历了些什么？

开发阶段，根据明确的需求，以及api文档，设计接口，开发，功能自测，对性能要求高的做压测，对可能会产生并发问题的做并发测试。发布时，对于新的服务，通过可视化页面配置动态路由，开启灰度发布，没问题后将新服务设置为正式版本（比如元数据中设置current标识为正式版本），关闭灰度发布。

#### 66.BIO、NIO、AIO有什么区别？

BIO：同步阻塞式IO，就是平常经常使用的IO

NIO：同步非阻塞式IO，通过channel，实现多路复用

AIO：NIO2，异步非阻塞IO，异步IO基于事件和回调机制

#### 67.Array和ArrayList有什么区别？

ArrayList不能放基本类型

ArrayList封装了更多方便的方法

#### 68.并行并发的区别？

并发是同一时刻多个进程同时跑，并发是同一时间间隔对**同一事物**的多个事件

#### 69.ThreadLocal是什么？有哪些应用场景？

用来解决多线程访问同一个共享变量时的并发问题

> ThreadLocal是jdk提供的，它提供了**线程本地变量**，也就是如果创建了一个ThreadLocal变量，那么访问这个变量的每一个线程都会有这个变量的一个本地副本。当多个线程操作这个变量时，实际操作的是自己本地内存里面的变量，从而避免了线程安全问题。创建一个ThreadLocal变量后，每个线程都会复制一个变量到自己的本地内存。

也就是说，业务中必须要使用一个全局变量，而又会涉及多线程，则可以使用ThreadLocal。

```java
public class ThreadLocalTest {
    static ThreadLocal<String> threadLocal = new ThreadLocal<>(){
        @Override
        protected String initialValue(){ return "initvalue"; }
    };
    public static void main(String[] args) {
        Thread threadOne = new Thread(new Runnable() {
            @Override
            public void run() {
                threadLocal.set("threadOne local");
                System.err.println("t1" + ": " +threadLocal.get());
            }
        });
        Thread threadTwo = new Thread(new Runnable() {
            @Override
            public void run() {
                threadLocal.set("threadTwo local");
                System.err.println("t2" + ": " +threadLocal.get());
            }
        });
        threadOne.start();// t1: threadOne local
        threadTwo.start();// t2: threadOne local
            Thread.sleep(2000);
        System.err.println(threadLocal.get());// initvalue
    }
}
```

#### 70.java内存模型

java内存模型规定，将所有的变量都放在主内存中，当线程使用变量时，会把主内存里的变量复制到自己的工作内存（cpu的一级缓存，或所有cpu共享的二级缓存，或cpu寄存器），线程读写变量时操作的是自己工作内存中的变量。

<img src="pictures\java内存模型.png">

#### 71.共享变量的内存可见性问题

双核cpu情景下，对于变量X，当两级缓存均空时，

线程A获取X发现没有则去主内存加载，更新到两级缓存中；

-->接着线程A执行X=1，将其写入两级缓存，并刷新主内存，此时两级缓存、主内存都是1

-->线程B获取X，一级缓存为空则去二级缓存获取，二级缓存命中了，更新到一级缓存；

-->线程B执行X=2，更新两级缓存，并刷新到主内存；

-->此时一切正常；

-->当线程A再次读取X时，对于A来讲，一级缓存不为空，X=1，直接返回。那么就有问题了，主内存明明是2才对。

这就是共享变量的内存不可见问题，**也就是变成B写入的值对线程A不可见**。

**而volatile可以保证可见性**，。

#### 72.synchronized的内存语义

进入synchronized的内存语义就是把再synchronized内使用到的变量从线程的工作内存中清除，这样synchronized块中使用到该变量时就不会从线程的工作内存中获取而是直接从主内存获取。退出synchronized块的内存语义是把再synchronized块内对共享变量的修改刷新到主内存。

**synchronized也可以保证共享变量内存的可见性，但会引起线程上下文切换，带来线程调度开销。**

#### 73.volatile可见性？

保证了内存可见性。

当一个变量被声明为volatile时，线程在写入变量时不会把值缓存在寄存器或者其他地方，而是把值刷新到主内存。其他线程读取该共享变量时，会从主内存重新获取最新值，而不是使用当前线程的工作内存的值。

#### 74.volatile能保证原子性吗？

不能

#### 75.volatile场景？

写入变量不依赖变量的当前值时。假如依赖，那么会有获取+计算+写入的过程，这个过程不是原子性的。

读写变量时没有加锁。锁本身保证了内存可见性，这时不需要用volatile。

#### 76.可重入锁的原理？

内部维护了一个线程标识，表示该锁被那个线程占用，然后关联一个计数器，初始为0，。线程获取了锁，计数器+1，释放则-1。当获取了该锁的线程再次获取时发现线程标识是自己，计数器+1。

#### 77.redis有哪些功能？

数据缓存

分布式锁

消息队列

数据持久化

------

分割（主要为了打印排版）





















#### 78.怎么让线程顺序执行

场景1：线程池的单例方式Executors.newSingleThreadExecutor(); 因为是单例的，只有当前线程执行完毕被回收到线程池中时，则塞队列中的下一个任务才会进来。

场景2：主线程jion

场景3：线程内部join上一个线程

场景4：配合object的 wait方法和notify方法以及同步关键字synchronized使用。

场景5：使用ReentrantLock的 lock() unlock() 方法（待尝试）

场景6： 使用CountDownLatch（cd），构造函数中传入int，指定初始值（内部是一个基于AQS的sync），使用cd.countDown()方法对其进行倒计时，第二个线程内显示使用cd.await()方法，当这个sync减到0时，继续执行代码，不然线程一直等待。

场景7：使用**CyclicBarrier**，

场景8：信号量机制**Sephmore**，acquire()方法请求许可，release()方法释放持有的许可。

https://blog.csdn.net/sinat_41832255/article/details/101148319

#### 79.redis几个数据类型的应用场景

String：

hash：对象信息，登录信息等

list：关注列表等

#### 80.CAS原理

CAS即“compare and swap”，首先检查某块内存的值是否跟之前我读取时的一样，不一样则表示期间此内存值已经被别的线程更改过，舍弃本次操作，否则说明期间没有其他线程对此内存值操作，可以把新的值设置给此内存。CAS具有原子性，原子性由CPU硬件指令实现保证，即使用JNI调用Native方法，调用硬件级别指令，JDK中提供了Unsafe类执行这些操作。

#### 81.jvm对java原生锁的优化？

java线程与操作系统的原生线程有映射关系，线程的阻塞或唤醒都需要操作系统协助，这就涉及用户态到内核态的转换。

自旋锁：线程阻塞前先自旋等待一段时间，可能在等待期间其他线程已经解锁，就无需让线程执行阻塞操作，避免用户态到内核态的切换。

偏向锁

轻量级锁

重量级锁

#### 82.补充12.synchronized与ReetrantLOck的区别

ReetrantLOck是Lock的实现类，互斥的同步锁

等待可中断

超时机制：过了一定的时间仍然无法获取则返回

可判断是否有线程在排队等待获取锁

可以相应中断请求：与synchronized不同，当获取到锁的线程被中断时能够相应中断，会抛出异常，并释放锁

可以实现公平锁



#### 83.java线程池原理？

看代码知道，它内部维护了一个HashSet，里面存放基于AQS实现的内部类Worker，而需要执行的任务放在阻塞队列 `BlockingQueue<Runnable> workQueue`  中。大致流程就是从这个队列中不断取出需要执行的任务，放到Workers中处理。



#### 84.线程池中的线程时怎么创建的？一开始就随着线程池的启动创建好的吗？

线程池默认初始化后不启动Worker，等待有请求时才启动。`execute()` 方法做了些啥？三个指标：

corePoolSize(核心线程数)、maximumPoolSize（最大线程数）、队列。

如果正在运行的线程数量小于corePoolSize，那么马上创建线程运行这个任务。

大于或等于corePoolSize，则把任务放进等待队列。队列满了之后，创建非核心线程处理任务。如果已达到最大线程数了，线程池会抛出异常 `RejectExecutionException` 

#### 85.ThreadLocal一定安全吗？（需要进一步加强理解）

内存泄漏问题。

ThreadLocal的实现基于一个ThreadLocalMap，key为弱引用的ThreadLocal实例，value为线程变量的副本。

https://juejin.cn/post/6844904046373896205

#### 86.乐观锁悲观锁



