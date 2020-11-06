# SELECT的执行顺序
> 查询是 RDBMS 中最频繁的操作。在理解 SELECT 语法的时候，还需要了解 SELECT 执行时的底层原理。只有这样，才能对 SQL 有更深刻的认识。在 SELECT 语句执行这些步骤的时候，每个步骤都会产生一个虚拟表，然后将这个虚拟表传入下一个步骤中作为输入。需要注意的是，这些步骤隐含在 SQL 的执行过程中，对于用户来说是不可见的。
---

## 1、执行顺序总结
```
1、FROM子句组装数据（包括通过 ON 进行连接）；
2、WHERE子句进行条件筛选；
3、GROUP BY分组；
4、使用聚集函数进行计算； 
5、HAVING筛选分组； 
6、计算所有的表达式； 
7、SELECT 的字段；
8、ORDER BY排序
9、LIMIT筛选
```

例如：
```
SELECT DISTINCT player_id, player_name, count(*) as num #顺序4、5
FROM player JOIN team ON player.team_id = team.team_id #顺序1
WHERE height > 1.80 #顺序2
GROUP BY player.team_id #顺序3
HAVING num > 2 #顺序6
ORDER BY num DESC #顺序7
LIMIT 2 #顺序8
```

1. SELECT 是先执行 FROM 这一步的。在这个阶段，如果是多张表联查，还会经历下面的几个步骤：
```
首先先通过 CROSS JOIN 求笛卡尔积，相当于得到虚拟表 vt（virtual table）1-1；
通过 ON 进行筛选，在虚拟表 vt1-1 的基础上进行筛选，得到虚拟表 vt1-2；
添加外部行。如果使用的是左连接、右链接或者全连接，就会涉及到外部行，也就是在虚拟表 vt1-2 的基础上增加外部行，得到虚拟表 vt1-3。
如果操作的是两张以上的表，还会重复上面的步骤，直到所有表都被处理完为止。这个过程得到是的原始数据
```

2. 拿到了查询数据表的原始数据，也就是最终的虚拟表 vt1，就可以在此基础上再进行 WHERE 阶段。在这个阶段中，会根据 vt1 表的结果进行筛选过滤，得到虚拟表 vt2。

3. GROUP在这个阶段中，实际上是在虚拟表 vt2 的基础上进行分组，得到中间的虚拟表 vt3

4. 使用聚集函数进行计算。

5. HAVING 阶段进行分组过滤，得到中间的虚拟表 vt4。 

6. 完成了条件筛选部分之后，就可以筛选表中提取的字段，也就是进入到 SELECT 和 DISTINCT 阶段。首先在 SELECT 阶段会提取想要的字段，然后在 DISTINCT 阶段过滤掉重复的行，分别得到中间的虚拟表 vt5-1 和 vt5-2

7. 提取了想要的字段数据之后，就可以按照指定的字段进行排序，也就是 ORDER BY 阶段，得到虚拟表 vt6

8. 最后在 vt6 的基础上，取出指定行的记录，也就是 LIMIT 阶段，得到最终的结果，对应的是虚拟表 vt7
---


## 2、limit(永远放在SQL语句的最后写)
> 作用：限制显示查询记录的记录个数

用法：
```
1、limit n -->显示几条记录
2、limit m, n 或 LIMIT n OFFSET m (m 表示从第几条记录开始显示，n 表示显示几条)
```

注意：
```
约束返回结果的数量可以减少数据表的网络传输量，也可以提升查询效率。如果知道返回结果只有 1 条，就可以使用LIMIT 1，告诉 SELECT 语句只需要返回一条记录即可。这样的好处就是 SELECT 不需要扫描完整的表，只需要检索到一条符合条件的记录即可返回。
如果数据表已经对字段建立了唯一索引，那么可以通过索引进行查询，不会全表扫描的话，就不需要加上LIMIT 1了
```
---


## 3. order by
> 作用：给查询的结果进行排序

用法：
```
1、语法格式：order by 字段名 排序方式;
2、排序方式：ASC(默认) 升序，DESC 降序
```

注意：
```
ORDER BY 后面可以有一个或多个列名，如果是多个列名进行排序，会按照后面第一个列先进行排序，当第一列的值相同的时候，再按照第二列进行排序，以此类推。

ORDER BY 可以使用非选择列进行排序，所以即使在 SELECT 后面没有这个列名，同样可以放到 ORDER BY 后面进行排序
```

```
关于ORDER BY字段是否增加索引：

在MySQL中，支持两种排序方式：FileSort和Index排序。Index排序的效率更高
Index排序：索引可以保证数据的有序性，因此不需要再进行排序。
FileSort排序：一般在内存中进行排序，占用CPU较多。如果待排结果较大，会产生临时文件I/O到磁盘进行排序，效率较低。
所以使用ORDER BY子句时，应该尽量使用Index排序，避免使用FileSort排序。
具体优化器是否采用索引进行排序，可以使用explain来进行执行计划的查看。

优化建议：
1、SQL中，可以在WHERE子句和ORDER BY子句中使用索引，目的是在WHERE子句中避免全表扫描，ORDER BY子句避免使用FileSort排序。某些情况下全表扫描，或者FileSort排序不一定比索引慢。但总的来说，我们还是要避免，以提高查询效率。一般情况下，优化器会帮我们进行更好的选择，当然我们也需要建立合理的索引。

2、尽量Using Index完成ORDER BY排序。如果WHERE和ORDER BY相同列就使用单索引列；如果不同使用联合索引。

3、无法Using Index时，对FileSort方式进行调优。
```

```
关于SELECT 语句不加 ORDER BY 如何排序:
SELECT 不加 “ORDER BY”时， MySQL 会尝试以尽可能快的方法返回数据。

MyISAM 表
MySQL Select 默认排序是按照物理存储顺序显示的。（不进行额外排序）
也就是说SELECT * FROM tbl – 会产生全表扫描。如果表没有删除、替换、更新操作，记录会显示为插入的顺序。

InnoDB 表
同样的情况，会按主键的顺序排列。这只是潜规则，是不靠谱的

结论：不能依赖 MySQL 的默认排序，想排序，总是加上 ORDER BY 的
```

```
ORDER BY 是对分的组排序还是对分组中的记录排序:

ORDER BY 就是对记录进行排序。如果你在 ORDER BY 前面用到了 GROUP BY，实际上这是一种分组的聚合方式，已经把一组的数据聚合成为了一条记录，再进行排序的时候，相当于对分的组进行了排序
```

```
在排序时, null 值被认为是最大的。 在降序排序时(descending)null值排在了最前面。解决这类问题有两种思路。

最简单的一种是用 coalesce 消除 null的影响，可以在输出时将 null 转换为 0：
SELECT name, coalesce(points, 0) FROM users ORDER BY 2 DESC; 

或输出时保留 null, 但排序时转换为 0: 
SELECT name,points FROM users ORDER BY coalesce(points, 0) DESC; 

还有一种方式需要数据库的支持（Oracle），指定排序时将 null 值放在最前面还是最后面: 
SELECT name,coalesce(points, 0) FROM users ORDER BY 2 DESC nulls last;
```
---


## 4、group by
> 作用：给查询的结果进行分组

注意：
```
1、group by后的字段名必须要为select之后的字段名
2、group by处理的是group by之后的所有字段，如果查询字段和group by之后的字段不一致，则必须要对该字段值做聚合处理(聚合函数)
```
---


## 5、having
> 作用：对查询的结果进行进一步筛选

注意：
```
1、having语句通常与group by语句联合使用，用来过滤由group by语句返回的记录集，作为筛选分组的条件。
2、having语句的存在弥补了where关键字后面不能与聚合函数联合使用的不足，where操作的是表中实际存在的字段，having操作的是聚合函数生成的显示列，WHERE子句用于对查询结果的分组前，通过WHERE来过滤。而HAVING子句用于筛选满足条件的组，用于在分组之后进行过滤
```
