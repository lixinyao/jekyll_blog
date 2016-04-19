---
layout: post
title: MySQL数据迁移到远程服务器
categories: [SQL]
tags:
- SQL
---

今天遇到一个问题，需将MySQL数据迁移到远程服务器，下面说操作步骤

# 本地全部数据导出

由于数据量很小，用mysqldump可以直接导出为一个文件

{% highlight sql %}
mysqldump -u username -p --all-databases > alldata.sql
{% endhighlight %}

# 本地ssh连接远程

{% highlight r %}
ssh username@ip
{% endhighlight %}

# 复制本地my.cnf到服务器

{% highlight r %}
ssh username@ip#登录远程服务器
sudo chmod 777 /etc#获取目录读写权限
exit#返回本地
sudo scp my.cnf remote_username@ip: /etc#复制文件到远程
{% endhighlight %}

# 将.sql文件复制到远程datadir

{% highlight sql %}
show variables like "datadir%";#查看数据存放目录
sudo scp /Users/lixinyao/alldata.sql remote_username@ip:datadir
{% endhighlight %}

# 更改配置文件并导入数据

## 更改my.cnf对于导入文件大小的限制

{% highlight sql %}
max_allowed_packet = 9999999M
{% endhighlight %}

## 导入数据

{% highlight sql %}
source datadir/data.sql
{% endhighlight %}
