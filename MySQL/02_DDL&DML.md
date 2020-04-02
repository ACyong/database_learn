# DDL&DML
> DDL&DML 是 DBMS 的核心组件，也是 SQL 的重要组成部分，DDL&DML 的正确性和稳定性是整个 SQL 运行的重要基础
---


## 1、针对数据库

1. 查看已有的库：
```
SHOW DATABASES;
```

2. 创建库：([]内是指定字符集，选填)
```
CREATE DATABASE 库名 [DEFAULT CHARSET = utf8]; 
或：
CREATE DATABASE IF NOT EXISTS 库名 [DEFAULT CHARSET utf8 COLLATE utf8_general_ci];
```

3. 查看创建库的语句：
```
SHOW CREATE DATABASE 库名;
```

4. 切换库：
```
USE 库名;
```

5. 查看当前所在库：
```
SELECT DATABASE();
```

6. 查看当前库中已有的表：
```
SHOW TABLES;
```

7. 删除库：
```
DROP DATABASE 库名;
```

注：COLLATE utf8_general_ci 代表的是数据库校对规则，utf8_bin将字符串中的每一个字符用二进制数据存储，区分大小写。utf8_genera_ci不区分大小写，ci为case insensitive的缩写，即大小写不敏感。utf8_general_cs区分大小写，cs为case sensitive的缩写，即大小写敏感。

---


## 2、针对数据表

1. 创建表：([]内选填)
```
DROP TABLE IF EXISTS 表名;
CREATE TABLE 表名(
    id INT(11) NOT NULL AUTO_INCREMENT,
    字段名 数据类型 [完整性约束条件],
    字段名 数据类型 [完整性约束条件],
    .....,
    PRIMARY KEY (`id`) USING BTREE
)[ENGINE = InnoDB AUTO_INCREMENT = 10069 CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic];
```

2. 查看创建表的语句（字符集）
```
SHOW CREATE TABLE 表名;
```

3. 查看表结构
```
DESC 表名;
```

4. 修改表名：
```
ALTER TABLE table_name RENAME table_new_name;
```

5. 删除表：
```
DROP TABLE 表名;
```

注：所有的数据都是以文件的形式存储在数据库目录下的数据库目录：/var/lib/mysql

---


## 3、针对表字段
> 语法：ALTER TABLE 表名 执行动作;

1. 添加字段(add)
```
1、添加字段到末尾：ALTER TABLE 表名 ADD 字段名 数据类型 [完整性约束条件];

2、添加字段到开始：ALTER TABLE 表名 ADD 字段名 数据类型 [完整性约束条件] FIRST;

3、添加字段到指定位置：ALTER TABLE 表名 ADD 字段名 数据类型 [完整性约束条件] AFTER 字段名;
```

2. 删除字段(drop)
```
ALTER TABLE 表名 DROP 字段名;
```

3. 修改数据类型(modify)
```
ALTER TABLE 表名 MODIFY 字段名 新的数据类型 [完整性约束条件];
```

4. 修改字段名(change)
```
ALTER TABLE 表名 CHANGE 旧名 新名 数据类型 [完整性约束条件];
```
---


## 4、表记录的管理：

1. 在表中插入记录([]内选填)
```
INSERT INTO 表名[字段名] VALUES(值1, 值2),.......;
```

2. 更新表记录
```
UPDATE 表名 SET 字段名 = 值, 字段名 = 值, ... WHERE 条件;

WHERE 条件子句（配合查、改、删操作）
```

3. 删除表记录
```
DELETE FROM 表名 WHERE 条件；
```

注：更新或删除如果不加where子句，将会把表中所有记录全部更改或删除

---


## 5、库\表\字段的命名规则：
```
1、可以使用数字、字母、_，但是不能是纯数字
2、库名区分字母大小写
3、库名具有唯一性
4、不能使用特殊字符和mysql关键字
```
