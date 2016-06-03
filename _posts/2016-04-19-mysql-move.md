---
layout: post
title: MySQL数据迁移到远程服务器
categories: [SQL]
tags:
- SQL
- 迁移数据
---

今天遇到一个问题，需将MySQL数据迁移到远程服务器，下面说操作步骤

# 本地全部数据导出

由于数据量很小，用mysqldump可以直接导出为一个文件

```
mysqldump -u username -p --all-databases > alldata.sql
```

# 本地ssh连接远程

```
ssh username@ip
```

# 复制本地my.cnf到服务器

```
ssh username@ip
sudo chmod 777 /etc
exit
sudo scp my.cnf remote_username@ip: /etc
```

# 将.sql文件复制到远程datadir

```
show variables like "datadir%";
sudo scp /Users/lixinyao/alldata.sql remote_username@ip:datadir
```

# 更改配置文件并导入数据

## 更改my.cnf对于导入文件大小的限制

```
max_allowed_packet = 9999999M
```

## 导入数据

```sql
source datadir/data.sql
```
