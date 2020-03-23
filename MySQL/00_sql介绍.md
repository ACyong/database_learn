# SQL 介绍：
> 结构化查询语句（Structured Query Language），SQL 用来和数据库打交道，完成和数据库的交互。SQL 是一套标准，但是每一个数据库都有自己才有的特性。
---


## 1、SQL 语句按功能划分

1. 数据定义语言（DDL-Date Definition Language），针对结构的操作
> 它用来定义我们的数据库对象，包括数据库、数据表和列。通过使用 DDL，可以创建，删除和修改数据库和表结构。代表关键字：CREATE、DROP 和 ALTER

2. 数据查询语句（DQL-Data Query Language），针对表中数据的操作
> 用它查询想要的记录，代表关键字：SELECT

3. 数据操作语言（DML-Data Manipulation Language），针对表中数据的操作
> 用它操作和数据库相关的记录，比如增加、删除、修改数据表中的记录。代表关键字：INSERT、DELETE、UPDATE

4. 数据控制语言（DCL-Data Control Language）
> 用它来定义访问权限和安全级别。代表关键字：GRANT、REVOKE

5. 事务控制语言（TCL-Transaction Control Language）
> 不同数据库情况不一样，以MySQL为例，代表关键字：COMMIT、ROLLBACK

> 注：好的代码规范是提高效率的关键，关于 SQL 大小写的问题
```
1、表名、表别名、字段名、字段别名等都小写；
2、SQL 保留字、函数名、绑定变量等都大写。
```
---
    

## 2、名词介绍：

1. DB（Database）就是数据库，存储数据的仓库，可以把它理解为多个数据表的集合。

2. DBMS（Database Management System），数据库管理系统或管理数据库的软件，DBMS = 多个数据库（DB） + 管理程序。

3. DBS（Database System），数据库系统，DBS = DB（存储）+ DBMS（数据库软件）+ 数据库应用 + 用户
---


## 3、MySQL 中的 SQL 是如何执行的

1. 连接层：客户端和服务器端建立连接，客户端发送 SQL 至服务器端；

2. SQL 层：对 SQL 语句进行查询处理；
```
1、查询缓存：Server 如果在查询缓存中发现了这条 SQL 语句，就会直接将结果返回给客户端；如果没有，就进入到解析器阶段。需要说明的是，因为查询缓存往往效率不高，所以在 MySQL8.0 之后就抛弃了这个功能。

2、解析器：在解析器中对 SQL 语句进行语法分析、语义分析。

3、优化器：在优化器中会确定 SQL 语句的执行路径，比如是根据全表检索，还是根据索引来检索等。

4、执行器：在执行之前需要判断该用户是否具备权限，如果具备权限就执行 SQL 查询并返回结果。在 MySQL8.0 以下的版本，如果设置了查询缓存，这时会将查询结果进行缓存。可以使用 select version(); 来查看 MySQL 的版本情况。
```

3. 存储引擎层：与数据库文件打交道，负责数据的存储和读取。常见的存储引擎：
```
1、InnoDB 存储引擎：它是 MySQL 5.5 版本之后默认的存储引擎，最大的特点是支持事务、行级锁定、外键约束等。

2、MyISAM 存储引擎：在 MySQL 5.5 版本之前是默认的存储引擎，不支持事务，也不支持外键，最大的特点是速度快，占用资源少。

3、Memory 存储引擎：使用系统内存作为存储介质，以便得到更快的响应速度。不过如果 mysqld 进程崩溃，则会导致所有的数据丢失，因此我们只有当数据是临时的情况下才使用 Memory 存储引擎。

4、NDB 存储引擎：也叫做 NDB Cluster 存储引擎，主要用于 MySQL Cluster 分布式集群环境，类似于 Oracle 的 RAC 集群。

5、Archive 存储引擎：它有很好的压缩机制，用于文件归档，在请求写入时会进行压缩，所以也经常用来做仓库。
```

注：由于MySQL的存储引擎各有特点，常用的有：MyISAM的使用场景为读写分离的读库， 而InnoDB为写库

---


## 4、如何在 MySQL 中对一条 SQL 语句的执行时间进行分析

1. 需要看下 profiling 是否开启，开启它可以让 MySQL 收集在 SQL 执行时所使用的资源情况，命令如下：
```
mysql> SELECT @@profiling;
```

2. profiling=0 代表关闭，我们需要把 profiling 打开，即设置为 1
```
mysql> SET profiling=1;
```

3. 执行一个 SQL 查询。

4. 查看当前会话所产生的所有 profiles:
```
mysql> SHOW profiles;
```

5. 如果想要获取上一次查询的执行时间，可以使用：
```
mysql> SHOW profile;
```

6. 查询指定的 Query ID
```
mysql> SHOW profile FOR QUERY 2;
```
