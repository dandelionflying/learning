clickHouse

1.环境搭建

> https://clickhouse.tech/docs/zh/getting-started/install/
>
> 推荐使用CentOS、RedHat和所有其他基于rpm的Linux发行版的官方预编译`rpm`包。
>
> 首先，您需要添加官方存储库：

```
sudo yum install yum-utils
sudo rpm --import https://repo.clickhouse.tech/CLICKHOUSE-KEY.GPG
sudo yum-config-manager --add-repo https://repo.clickhouse.tech/rpm/stable/x86_64

```

> 如果您想使用最新的版本，请用`testing`替代`stable`(我们只推荐您用于测试环境)。`prestable`有时也可用。
>
> 然后运行命令安装：

```
sudo yum install clickhouse-server clickhouse-client
```

2.启动

如果没有`service`，可以运行如下命令在后台启动服务：

```
$ sudo /etc/init.d/clickhouse-server start
```

日志文件将输出在`/var/log/clickhouse-server/`文件夹。

如果服务器没有启动，检查`/etc/clickhouse-server/config.xml`中的配置。

您也可以手动从控制台启动服务器:

```
$ clickhouse-server --config-file=/etc/clickhouse-server/config.xml
```

在这种情况下，日志将被打印到控制台，这在开发过程中很方便。

如果配置文件在当前目录中，则不需要指定`——config-file`参数。默认情况下，它的路径为`./config.xml`。

ClickHouse支持访问限制设置。它们位于`users.xml`文件(与`config.xml`同级目录)。
默认情况下，允许`default`用户从任何地方访问，不需要密码。可查看`user/default/networks`。
更多信息，请参见[Configuration Files](https://clickhouse.tech/docs/zh/operations/configuration-files/)。

启动服务后，您可以使用命令行客户端连接到它:

```
$ clickhouse-client
```

默认情况下，使用`default`用户并不携带密码连接到`localhost:9000`。还可以使用`--host`参数连接到指定服务器。



**启动报错：**

```
Code: 210. DB::NetException: Connection refused
```

参考博客方法

将配置文件中

将<listen_host>0.0.0.0</listen_host>改成
<listen_host>::</listen_host>

**无用×**

实际上是上一个错误，如图：

![](D:/zzx/codes/learning/pictures/clickhouse启动.png)

使用命令

```
systemctl start clickhouse-server.service
```

替换先前的

```
sudo /etc/init.d/clickhouse-server start
```

查看status确认服务正常：

```
systemctl status clickhouse-server.service
```

启动正常

![](D:/zzx/codes/learning/pictures/clickhouse启动2.png)

3.示例数据库使用

下载并提取表数据[ ](https://clickhouse.tech/docs/zh/getting-started/tutorial/#download-and-extract-table-data)

```
curl https://datasets.clickhouse.tech/hits/tsv/hits_v1.tsv.xz | unxz --threads=`nproc` > hits_v1.tsv
curl https://datasets.clickhouse.tech/visits/tsv/visits_v1.tsv.xz | unxz --threads=`nproc` > visits_v1.tsv
```

创建表[ ](https://clickhouse.tech/docs/zh/getting-started/tutorial/#create-tables)

与大多数数据库管理系统一样，ClickHouse在逻辑上将表分组为数据库。包含一个`default`数据库，但我们将创建一个新的数据库`tutorial`:

```
clickhouse-client --query "CREATE DATABASE IF NOT EXISTS tutorial"
```

创建数据库失败，官方示例数据库sql有问题

![](D:/zzx/codes/learning/pictures/clickhouseError.png)

**解决：**

官网的示例中字段名使用  **`** 包裹，实际不需要（单双引号也不需要），而且，页面复制过来的语句每行末尾包含了换行符，在clickclient中直接跑会识别出换行符导致语句被错误分段执行，导致报错，需要在其他工具中把换行符替换成空格。

结果成功：

![](D:/zzx/codes/learning/pictures/clickhouseCreateTable.png)

4.导入示例数据

数据导入到ClickHouse是通过[INSERT INTO](https://clickhouse.tech/docs/zh/sql-reference/statements/insert-into/)方式完成的，查询类似许多SQL数据库。然而，数据通常是在一个提供[支持序列化格式](https://clickhouse.tech/docs/zh/interfaces/formats/)而不是`VALUES`子句（也支持）。

我们之前下载的文件是以制表符分隔的格式，所以这里是如何通过控制台客户端导入它们:

```
clickhouse-client --query "INSERT INTO tutorial.hits_v1 FORMAT TSV" --max_insert_block_size=100000 < hits_v1.tsv
clickhouse-client --query "INSERT INTO tutorial.visits_v1 FORMAT TSV" --max_insert_block_size=100000 < visits_v1.tsv
```

> ClickHouse有很多[要调整的设置](https://clickhouse.tech/docs/zh/operations/settings/)在控制台客户端中指定它们的一种方法是通过参数，就像我们看到上面语句中的`--max_insert_block_size`。找出可用的设置、含义及其默认值的最简单方法是查询`system.settings` 表:
>
> ```
> SELECT name, value, changed, description
> FROM system.settings
> WHERE name LIKE '%max_insert_b%'
> FORMAT TSV
> 
> max_insert_block_size    1048576    0    "The maximum block size for insertion, if we control the creation of blocks for insertion."
> ```

