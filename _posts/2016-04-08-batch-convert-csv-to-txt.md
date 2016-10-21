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
mylist = lapply(all.files,function(i) read_csv(i,locale = locale(encoding = "GB18030")))
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

# 关于encoding

中文编码一般选 `GB18030`，这个应该是完全兼容`GBK`

[hadley大神帮解决问题还是很开心的！](https://github.com/hadley/readr/issues/438)

[以后解决识别文件编码问题](http://www.vim.org/scripts/script.php?script_id=1708)
