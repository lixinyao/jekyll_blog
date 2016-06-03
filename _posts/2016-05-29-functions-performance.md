---
layout: post
title: R函数的性能比较
categories: [R]
tags:
- R
- dbFetch
- dbGetQuery
- read.table
- fread
---

# 测试环境

## 电脑配置

- 2015年初MacBook Pro
- 系统 OS X EI Capitan 10.11.4
- 处理器 2.7G  GHz Inter Core i5
- 内存 8G 1867 MHz DDR3
- 硬盘 256G SSD

## R版本

- R version 3.2.2 (2015-08-14) -- "Fire Safety"
- Platform: x86_64-apple-darwin13.4.0 (64-bit)

# dbFetch & dbGetQuery

18335813行、41列的MySQL表读入R数据框。`dbGetQuery`稍快，比`dbFetch`快10%

```r
> system.time(dbFetch(res1,n=-1))
用户    系统    流逝
155.731  79.814 286.987
> system.time(dbGetQuery(con1,"select * from ..."))
用户    系统    流逝
140.896  85.054 263.626
```

# read.table & fread & read_tsv

3.5G的txt读入R，`fread`比`read.table`性能提升7倍，比hadley大神readr包里的`read_tsv`提升1倍

{% highlight r %}
> system.time(read.table("mydata1.txt",encoding = 'UTF-8',stringsAsFactors=FALSE,header=TRUE,sep='\t'))
用户    系统    流逝             
532.444 193.950 810.850
> system.time(fread("mydata.txt",encoding = 'UTF-8',stringsAsFactors=FALSE))
用户   系统   流逝
64.570 17.121 97.714
> system.time(read_tsv("mydata1.txt"))
用户    系统    流逝
156.835  25.569 204.533
{% endhighlight %}
