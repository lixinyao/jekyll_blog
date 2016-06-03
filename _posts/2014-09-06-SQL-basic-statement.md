---
layout: post
title: SQL基本语句
categories: [SQL]
tags:
- SQL
- 增删改查
---

简单介绍`SELECT`,`INSERT`,`UPDATE`,`DELETE`四大基本语句

# SELECT

## SELECT和FROM

```sql
SELECT * FROM table;
SELECT <col> FROM dtable;
```

## WHERE

WHERE是限定条件，查询更快

```sql
SELECT <col> FROM table WHERE <restrictive condition>;
```

WHERE子句运算符

1. `=`, `>`, `<`, `!=`, `>=`等比较运算符
2. `NOT`, `AND`, `OR`等逻辑运算符， 在`WHERE`后结合条件
3. `BETWEEN`
4. `LIKE`, `%`, `_`结合使用
5. `IN`
6. `EXISTS`

## ORDER BY

```sql
SELECT <col>
FROM table
WHERE <condition>
ORDER BY <col1>, <col2> DESC;
```

## GROUP BY和聚合函数

`GROUP BY`放在`ORDER BY`之前，常和`SUM`,`MIN`,`MAX`,`COUNT`一起使用，如查询`sales`表`ID`大于`5`的员工的销售量`QTY`，且按销售量排序，可写为

```sql
SELECT ID,COUNT(QTY)
FORM sales
WHERE ID > 5
GROUP BY ID
ORDER BY COUNT(QTY) DESC;
```

## HAVING

`HAVING`和`GROUP BY`配合使用，在上例中加**销量大于2000**的条件

```sql
SELECT ID,COUNT(QTY)
FORM sales
WHERE ID > 5
GROUP BY ID
HAVING COUNT(QTY) > 2000
ORDER BY COUNT(QTY) DESC;
```

## DISTINCT

表示去除重复项，假设销售表里`ID`不是主键，而且`ID`有重复，现在挑出不重复的`ID`

```sql
SELECT DISTINCT(ID)
FROM sales;
```

# INSERT

```sql
INSERT INTO table
(col1,col2,...)
VALUES
(value1,value2,...)
```

# UPDATE

`UPDATE`表示更新，常和`WHERE`一块使用

```sql
	UPDATE table
	SET Name = "lixinyao"
	WHERE ID = 79110;
```

# DELETE

删除`ID`为79110的记录

```sql
DELETE table
WHERE ID =79110;
```
