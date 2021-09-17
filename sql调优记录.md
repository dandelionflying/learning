#### 1.避免全表扫描

（1）sql的on子句或where子句涉及到的列上没有索引

（2）表数量很小，走索引比全表扫描效率更低

#### 2.避免索引失效

（1）不在索引上做任何操作（计算、函数、自动或手动类型转换），会导致索引失效。

（2）存储引擎不能使用索引中范围条件右边的列。

（3）尽量使用覆盖索引（索引列与查询列一致），不使用select *。 

（4）mysql使用不等于时不走索引。

（5）mysql使用is （not） null时不走索引。

（6）mysql中 like通配符使用“%xxx”前置匹配，不走索引。

#### 3.避免排序，不能避免的话，尽量选择索引排序

#### 4.避免查询不必要的字段

#### 5.避免临时表的创建，删除

#### 6.经常需要排序的列上加索引

#### 7.查询数据库所有表的记录数

```mysql
select table_schema, table_name,table_rows
from information_schema.tables
where table_schema='aobao'
ORDER BY table_rows desc
```

#### 8.将打算加索引的列设置为not null，否则索引失效导致全表扫描

查询未被使用的索引

```mysql
select * from sys.schema_unused_indexes
```

#### 9.要避免冗余索引

比如 indexA(name)和indexB(name,age)，**能命中indexA就一定能命中indexB**

```mysql
select * from sys.schema_redundant_indexes
```

#### 10.要限定数据的范围

如查询订单历史时，控制在一个月的范围内

#### 11.读写分离



#### 12.sql优化原则

先从explain和profile `show profiles;` 入手，profiling可以用来准确定位一条sql的性能瓶颈

永远用**小结果集驱动大的结果集**

在索引中完成排序

使用最小Columns

使用最有效的过滤条件

避免复杂的join和子查询

#### 13.join的原理

mysql中使用 Nested Loop Join （嵌套循环join）来实现join。通过A表的结果集作为循环基础，一条一条的通过结果集中的数据作为过滤条件到下一个表中查询数据，然后合并结果。如果还有更多的表参与，则通过前面join之后的结果集作为循环数据的基础，再去进行合并。

join优化原则：

尽可能减少join语句中的Nested Loop的循环总次数，用小结果集驱动大结果集

优先优化Nested Loop的内层循环

保证join语句中被驱动表上join条件字段已经被索引

扩大join buffer的大小

#### 14.mysql执行流程

查询缓存

解析器生成解析数

预处理再次生成解析数

查询优化器

查询执行计划

查询执行引擎

查询数据返回结果

#### 15.表结构对性能的影响

冗余数据的处理：

​	每一列只能有一个值

​	每一行可以被唯一区分

​	不包含其他表的已包含的非关键信息

大表拆小表：

​	一般不会设计属性过多的表

​	一般不会超过500到1000万数据的表

​	有大数据的列单独拆为小表

根据需求展示更加合理的表结构

常用属性分离为小表

#### 16.索引创建技巧

较频繁的作为查询条件的字段应该创建索引。

唯一性太差的字段不适合单独创建索引，即使频繁的将其作为查询条件。因为他不能有效地区分数据，比如accountType有14种，那么最多只能按照1/14的比例过滤数据，对性能提升不大。

更新频繁的字段不适合创建索引，会增加索引的维护成本。

不出现在where子句中的字段，不需要创建索引。

索引当然不是越多越好。

#### 17.union all 一般比union效率更高，因为没有去重的过程

当能够确定两个结果集没有重复数据时，就可以用union alll

#### 18.字符串类型字段不加单引号，索引失效

#### 19.如果查询包含group by ，但用户想要避免排序结果的消耗，可以加上order by null 禁止排序

`select age,count(*) from emp group by age order by null;`

#### 20.filesort：不通过索引直接排序的情况，filesort效率很低

减少额外排序，通过索引直接返回有序数据。

where条件和order by 使用相同的索引

orderby 的顺序和索引顺序相同

order by 的字段要么都是升序要么都是降序，不然内部会有额外的操作，会出现filesort

#### 21.or 可以用union替代

https://www.cnblogs.com/gttttttt/p/13681128.html

