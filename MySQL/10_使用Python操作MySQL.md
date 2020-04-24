# 使用Python操作MySQL
> Python 可以支持非常多的数据库管理系统，比如 MySQL、Oracle、SQL Server 和 PostgreSQL 等。为了实现对这些 DBMS 的统一访问，Python 需要遵守 DB API 规范。在这个规范提供了数据库对象连接、对象交互和异常处理的方式，为各种 DBMS 提供了统一的访问接口。这样做的好处就是如果项目需要切换数据库，Python 层的代码移植会比较简单。
---


## 1、首先安装 mysql-connector，它是 MySQL 官方提供的驱动器
```
pip install mysql-connector
```

在安装之后，可以创建数据库连接，然后查看下数据库的版本号，来验证下数据库是否连接成功
```
# -*- coding: UTF-8 -*-
import mysql.connector


# 打开数据库连接
db = mysql.connector.connect(
       host="localhost",
       user="root",
       passwd="XXX", # 写上你的数据库密码
       database='wucai', 
       auth_plugin='mysql_native_password'
)

# 获取操作游标 
cursor = db.cursor()

# 执行SQL语句
cursor.execute("SELECT VERSION()")

# 获取一条数据
data = cursor.fetchone()

print("MySQL版本: %s " % data)
# 关闭游标&数据库连接

cursor.close()
db.close()
```
---


## 2、Connection 和 Cursor

Connection 就是对数据库的当前连接进行管理，可以通过它来进行以下操作：
```
通过指定 host、user、passwd 和 port 等参数来创建数据库连接，这些参数分别对应着数据库 IP 地址、用户名、密码和端口号；
使用 db.close() 关闭数据库连接；
使用 db.cursor() 创建游标，操作数据库中的数据；
使用 db.begin() 开启事务；
使用 db.commit() 和 db.rollback()，对事务进行提交以及回滚。
```

通过cursor = db.cursor()创建游标后，就可以通过面向过程的编程方式对数据库中的数据进行操作：
```
使用cursor.execute(query_sql)，执行数据库查询；
使用cursor.fetchone()，读取数据集中的一条数据；
使用cursor.fetchall()，取出数据集中的所有行，返回一个元组 tuples 类型；
使用cursor.fetchmany(n)，取出数据集中的多条数据，同样返回一个元组 tuples；
使用cursor.rowcount，返回查询结果集中的行数。如果没有查询到数据或者还没有查询，则结果为 -1，否则会返回查询得到的数据行数；
使用cursor.close()，关闭游标。
```
---


## 3、对数据表进行增删改查

