---
layout: post
title: R语言批量处理文件
categories: [R]
tags:
- R
- 批处理
---

# R读取文件夹下文件转化为数据框

```r
# 读入文件名
all.files = list.files(path = "yourpath",
                       full.name = TRUE,
                       pattern = ".csv")
# 读入数据
mylist = lapply(all.files,function(i) read.csv(i))
# 合并为数据框
mydata = do.call('rbind',mylist)
head(mydata)
dim(mydata)
# 输出txt
write.table(mydata,file="yourpath",
            quote = FALSE,
            row.names = FALSE,
            sep = "\t",
            fileEncoding = "UTF-8")
```
