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
```
import json
import traceback
import mysql.connector

# 读取数据库链接配置文件, 建议把数据库链接信息写到配置文件里，防止密码泄露。
with open('mysql.json', encoding='utf-8') as con_json:
    con_dict = json.load(con_json)

# 打开数据库链接
db = mysql.connector.connect(
    host=con_dict['host'],
    user=con_dict['user'],
    passwd=con_dict['passwd'],
    database=con_dict['database'],
    auth_plugin=con_dict['auth_plugin'],  # 密码验证方式
)

# 获取操作游标
cursor = db.cursor()
try:
    sql = 'SELECT statement'
    cursor.execute(sql)
    data = cursor.fetchall()
    print(cursor.rowcount, '查询成功。')
    for each in data:
        print(each)
except Exception as e:
    # 打印异常信息
    traceback.print_exc()
finally:
    cursor.close()
    db.close()
```

使用 cursor.execute 来执行相应的 SQL 语句, 执行 insert, delete, update, 的时候使用 db.commit() 和 db.rollback()，对事务进行提交以及回滚, 执行 select 的时候使用cursor.fetchone(), cursor.fetchall(), cursor.fetchmany(n) 取数据


## 4. 使用Python ORM框架来操作MySQL(SQLAlchemy)
```
# 获取基类, 文件路径: models/base.py
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()
```

```
# 获取 session, 文件路径: libs/mysql/client.py
from sqlalchemy import create_engine
from sqlalchemy.orm import scoped_session, sessionmaker

from config.config import URI, POOL_SIZE, MAX_OVER, \
    POOL_RECYCLE, POOL_TIMEOUT

engine = create_engine(
    URI,
    pool_size=POOL_SIZE,
    max_overflow=MAX_OVER,
    pool_recycle=POOL_RECYCLE,
    pool_timeout=POOL_TIMEOUT)


def get_session():
    return scoped_session(sessionmaker(bind=engine))()
```

```
# 定义User对象文件路径:models/user.py
from datetime import datetime

from libs.mysql.client import get_session
from models.base import Base


class User(Base):
    # 表的名字:
    __tablename__ = "user"

    # 表的结构:
    id = Column(Integer, primary_key=True, autoincrement=True)
    user_id = Column(Integer)
    name = Column(String(64))
    create_time = Column(DateTime, default=datetime.now)

    @classmethod
    def create(cls, user_id, name):
        # 创建user对象:
        user = cls(user_id=user_id, name=name)
        # 获取session 对象
        session = get_session()
        # 添加到session
        session.add(user)
        # 提交即保存到数据库, 自动关闭session, 没有commit 需要手动关闭session: session.close()
        session.commit()
```

在 SQLAlchemy 中，采用 Column 对字段进行定义，常用的数据类型如下：
| 类型     | 解释    |
| ------- | ------- |
| Integer | 整数类型 |
| Float   | 浮点类型 |
| Decimal | 定点类型 |
| BOOlean | 布尔类型 |
| Date    | datetime.date 日期类型 |
| Time    | datetime.time 时间类型 |
| String  | 字符类型, 使用时需要制定长度, 区别于Text类型 |
| Text    | 文本类型 |

除了指定 Column 的数据类型以外，还可以指定 Column 的参数，这些参数可以帮我们对对象创建列约束：
| 约束     | 解释    |
| ------- | ------- |
| default | 默认值   |
| primary_key | 是否为主键 |
| unique  | 是否唯一 |
| autoincrement | 是否自动增长 |
