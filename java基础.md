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