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



