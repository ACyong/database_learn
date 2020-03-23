# DDL
> DDL 是 DBMS 的核心组件，也是 SQL 的重要组成部分，DDL 的正确性和稳定性是整个 SQL 运行的重要基础
---


## 1、针对数据库

库的基本操作
```
1、查看已有的库：
SHOW DATABASES;

2、创建库：([]内是指定字符集，选填)
CREATE DATABASE 库名 [DEFAULT CHARSET = UTF8];

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

表的基本操作
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


## 3、库的命名规则：
```
1、可以使用数字、字母、_，但是不能是纯数字
2、库名区分字母大小写
3、库名具有唯一性
4、不能使用特殊字符和mysql关键字
```
---


## 练习1：

1. 创建库stu，指定字符集utf-8
```
CREATE DATABASE stu DEFAULT CHARSET = UTF8;
```

2. 进入到库stu中
```
USE stu;
```

3. 查看当前所在库
```
SELECT DATABASE();
```

4. 查看库中已有的表
```
SHOW TABLES;
```

5. 查看stu的字符集
```
SHOW CREATE DATABASE stu;
```

6. 删除库stu
```
DROP DATABASE stu;
```
---


## 练习2：

1. 创建数据库stu ，指定默认编码为utf8
```
CREATE DATABASE stu DEFAULT CHARSET = UTF8;
```

2. 创建表stuinfo, utf8, 字段要求：
```
学号：id 要求显示宽度为3，位数不够用0填充，主键自增长
姓名：name：name 变长， 宽度20
年龄：age：微小整型， 不能输入负数
身高：height 浮点型， 小数位2位
工资：salary 浮点型，小数位2位， 最大值99999.99

USE stu;

CREATE TABLE stuinfo(
    id INT(3) UNSIGNED ZEROFILL PRIMARY KEY AUTO_INCREMENT,
    user_name VARCHAR(20),
    age TINYINT UNSIGNED,
    height FLOAT(5, 2),
    salary FLOAT(7, 2)
)DEFAULT CHARSET = "UTF8";
```

3. 插入一条记录
```
INSERT INTO stuinfo(user_name, age, height, salary) VALUES('张三', 23, 170.12, 30000.99);
```
