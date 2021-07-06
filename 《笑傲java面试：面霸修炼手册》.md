《笑傲java面试：面霸修炼手册》

java8steam

1.为一组序列提供顺序、并行运算

> 流：随时间产生的数据序列

​	1）支持函数式编程

​	2）提供管道运算能力（**一个计算过程的输出作为另一个计算过程的输入**）

​	3）提供并行parallel计算能力

​	4）提供大量的操作

2.提供流计算

![](pictures/管道（pipeline）.png)



​	1）操作：
​		（1）状态：

​			a.有状态：sorted skip limit 任何处罚状态变化的程序

​			b.无状态：map reduce

​		（2）副作用：

​			a.纯函数:没有副作用的函数

​			b.非纯函数

> lambda表达式：相当于一种匿名的函数

3.提供并发计算

![并发计算流程.png](pictures/并发计算流程.png)

4.函数式基本概念

![函数式基本概念.png](pictures/函数式基本概念.png)

5.monad

> 一个自函子范畴的一个幺半群

（1）monada设计模式特点

- 一个泛型的构造函数，如Optional<T>
- 不改变泛型类型的运算操作，内部是非泛型计算

> **例：可以改变其中的数据类型，但不能改变本身的Stream类型**
>
> ```java
> // Stream<String>
> var result = Stream.of("Hello", "World")
>         .map(String::length);
> ```

> 一个比较不错的例子
>
> https://www.jdon.com/idea/java8-monad.html

- 泛型类型不变。

> 比如可以使Optional<Integer>到Optional<String> 但还是Optional<T>

