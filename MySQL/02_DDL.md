# DDL
> DDL 是 DBMS 的核心组件，也是 SQL 的重要组成部分，DDL 的正确性和稳定性是整个 SQL 运行的重要基础
---


## 1、针对数据库

1. 库的基本操作
```
1、查看已有的库：
SHOW DATABASES;

2、创建库：([]内是指定字符集，选填)
CREATE DATABASE 库名 [DEFAULT CHARSET = utf8];

3、查看创建库的语句：
SHOW CREATE DATABASE 库名;

4、切换库
USE 库名;

5、查看当前所在库：
SELECT DATABASE();

6、查看当前库中已有的表：
SHOW TABLES;

7、删除库：
DROP DATABASE 库名;
```
---


## 2、针对数据库

1. 表的基本操作
```
1、创建表: ([]内是指定字符集，选填)
CREATE TABLE 表名(
    字段名 数据类型 [完整性约束条件],
    字段名 数据类型 [完整性约束条件],
    .....
)[DEFAULT CHARSET = utf8];

2、查看创建表的语句（字符集）
SHOW CREATE TABLE 表名;

3、查看表结构
DESC 表名;

4、修改表名：
ALTER TABLE table_name RENAME table_new_name;

5、删除表：
DROP TABLE 表名;
```
注：所有的数据都是以文件的形式存储在数据库目录下的数据库目录：/var/lib/mysql

---


## 库的命名规则：
```
1、可以使用数字、字母、_，但是不能是纯数字
2、库名区分字母大小写
3、库名具有唯一性
4、不能使用特殊字符和mysql关键字
```
