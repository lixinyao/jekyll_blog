---
layout: post
title: MySQL数据类型的选择
categories: [SQL]
tags: [SQL]
---
本文介绍MySQL数据类型的选择，涉及字符型、数字型、日期型

# char和varchar

我们分别从存储和检索两个方面查看char和varchar的区别，例子来源于《深入浅出MySQL》

## 存储

char为定长，varchar为变长。两者区别如下表：

| values    | char(4)    | storage | varchar(4) | storage |
| :-------- | :--------- | :------ | :--------- |:------  |
| 'ab'      | 'ab  '     | 4个字节  | 'ab '      | 3个字节  |
| ''        | '    '     | 4个字节  | ''         | 1个字节  |

## 检索

以下例子可以看出，char类型在检索时会去除尾部的空格

{% highlight sql %}
mysql> create table vc(v varchar(4),c char(4));
Query OK, 0 rows affected (0.08 sec)

mysql> insert into vc values ('ab ','ab ');
Query OK, 1 row affected (0.05 sec)

mysql> select concat(v,'+'),concat(c,'+') from vc;
+---------------+---------------+
| concat(v,'+') | concat(c,'+') |
+---------------+---------------+
| ab + | ab+ |
+---------------+---------------+
1 row in set (0.00 sec)
{% endhighlight %}

## char和varchar的选择

MyISAM存储引擎：建议使用char代替varchar（固定长度处理速度快）
InnoDB存储引擎：建议使用varchar。InnoDB数据表，内部的行存储格式没有区分固定长度和可变长度，因此本质上使用char不一定比varchar性能好。另外varchar占用空间少

# text与blob

一般在保存少量字符串的时候，使用varchar和char，而保存大文本时，则使用text或blob。二者的区别是：blob能保存二进制文件（如图片），text只能保存字符型数据。text和blob分别有三种，text(blob)、medium、long，区别在于可存储字节长度

## optimize table

text和blob值在执行了删除操作后会留下大量的空洞，导致性能问题。定期使用`optimize table`进行碎片整理，能避免性能问题

{% highlight sql %}
optimize table tablename;
{% endhighlight %}

## 合成索引提升大文本的精确匹配性能

{% highlight sql %}
mysql> create table t (id varchar(100),context blob,hash_value varchar(40));
Query OK, 0 rows affected (0.08 sec)

mysql> insert into t values(1,repeat('beijing',2),md5(context));
Query OK, 1 row affected (0.07 sec)

mysql> insert into t values(2,repeat('beijing',2),md5(context));
Query OK, 1 row affected (0.04 sec)

mysql> insert into t values(3,repeat('beijing',2),md5(context));
Query OK, 1 row affected (0.05 sec)

mysql> insert into t values(4,repeat('beijing2008',2),md5(context));
Query OK, 1 row affected (0.04 sec)

mysql> select * from t;
+------+------------------------+----------------------------------+
| id | context | hash_value |
+------+------------------------+----------------------------------+
| 1 | beijingbeijing | 09746eef633dbbccb7997dfd795cff17 |
| 2 | beijingbeijing | 09746eef633dbbccb7997dfd795cff17 |
| 3 | beijingbeijing | 09746eef633dbbccb7997dfd795cff17 |
| 4 | beijing2008beijing2008 | 0fe88accc8741a9d1bc323bd286866bb |
+------+------------------------+----------------------------------+
4 rows in set (0.00 sec)

mysql> select * from t where hash_value = md5(repeat('beijing2008',2));
+------+------------------------+----------------------------------+
| id | context | hash_value |
+------+------------------------+----------------------------------+
| 4 | beijing2008beijing2008 | 0fe88accc8741a9d1bc323bd286866bb |
+------+------------------------+----------------------------------+
1 row in set (0.00 sec)

{% endhighlight %}

## 前缀索引进行模糊查询

只为字段的前几列创建索引，模糊匹配

{% highlight sql %}
create index idx_blob on t(context(100));
{% endhighlight %}

# 浮点数和定点数

浮点数一般为单精度float和双精度double。如要插入数据的精度大于定义的精度，则会保存为四舍五入后的值
定点数一般为decimal，以字符串形式存储
**以浮点数存储数据可能会存在误差**

{% highlight sql %}
mysql> create table test (c1 float(10,2),c2 decimal(10,2));
Query OK, 0 rows affected (0.08 sec)

mysql> insert into test values(131072.32,131072.32);
Query OK, 1 row affected (0.04 sec)

mysql> select * from test;
+-----------+-----------+
| c1 | c2 |
+-----------+-----------+
| 131072.31 | 131072.32 |
+-----------+-----------+
1 row in set (0.00 sec)
{% endhighlight %}

# 日期类型

日期类型有date、time、datetime、timestamp。选择时要选择满足应用的最小存储的日期类型
