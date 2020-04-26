# sql函数
> 在 SQL 语言中，包括了内置函数和自定义函数。内置函数是系统内置的通用函数。
---

## 1、算术函数

| 函数名   | 定义    |
| ------- | -------|
| ABS()   | 取绝对值 | 
| MOD()   | 取余 | 
| ROUND() | 四舍五入为指定额小数位，需要两个参数，分别是字段名和小数位 | 

例：
```
SELECT ABS(-2)，运行结果为 2。
SELECT MOD(101,3)，运行结果 2。
SELECT ROUND(37.25,1)，运行结果 37.3。
```

## 2、字符串函数

| 函数名   | 定义    |
| ------- | -------|
| CONCAT()   | 将多个字符拼接起来 | 
| LENGTH()   | 计算字段长度，一个汉字算三个长度，一个数字或字母算一个长度 | 
| CHAR_LENGTH() | 计算字段长度，一个汉字、数字或字母算一个长度 | 
| LOWER()   | 将字符串中的字符转换为小写 | 
| UPPER()   | 将字符串中的字符转换为大写    | 
| REPLACE()   |     | 
| SUBSTRING()   |     | 

例：
```
SELECT CONCAT('abc', 123)，运行结果为 abc123。
SELECT LENGTH('你好')，运行结果为 6。
SELECT CHAR_LENGTH('你好')，运行结果为 2。
SELECT LOWER('ABC')，运行结果为 abc。
SELECT UPPER('abc')，运行结果 ABC。
SELECT REPLACE('fabcd', 'abc', 123)，运行结果为 f123d。
SELECT SUBSTRING('fabcd', 1,3)，运行结果为 fab。
```

## 3、日期函数

例：
```
SELECT CURRENT_DATE()，运行结果为 2019-04-03。
SELECT CURRENT_TIME()，运行结果为 21:26:34。
SELECT CURRENT_TIMESTAMP()，运行结果为 2019-04-03 21:26:34。
SELECT EXTRACT(YEAR FROM '2019-04-03')，运行结果为 2019。
SELECT DATE('2019-04-01 12:00:05')，运行结果为 2019-04-01。
```

## 4、转换函数

例：
```
SELECT CAST(123.123 AS INT)，运行结果会报错。
SELECT CAST(123.123 AS DECIMAL(8,2))，运行结果为 123.12。
SELECT COALESCE(null,1,2)，运行结果为 1。
```
