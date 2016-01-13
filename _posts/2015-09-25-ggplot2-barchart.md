---
layout: post
title: ggplot2与条形图
categories: [R]
tags: [ggplot2]
---

最近一段时间，准备把ggplot2系统的学习一下，避免做事儿的时候还要临时抱佛脚，临时看help。学习手册就是大家熟知的入门级的《R Graphics Cookbook》，基本上就是在这里记录一下自己的学习历程，并且加入一些自己的理解。那我们就从最简单的条形图开始ggplot2的学习之旅吧！

条形图分为以下几类：

1. 简单条形图
2. 分组条形图
3. 堆积条形图
4. 百分比堆积条形图
5. 克利夫兰点图

接下来我们就开始详细介绍，先把该加载的包都加上

{% highlight r %}
library(ggplot2)
library(gcookbook)
{% endhighlight %}

# 简单条形图

简单条形图分为两类，一个是按数值统计的，一个是按数量统计的。

## 按数值统计

以BOD数据集为例，一列是`Time`，一列是`demand`，做条形图

{% highlight r %}
> BOD
  Time demand
1    1    8.3
2    2   10.3
3    3   19.0
4    4   16.0
5    5   15.6
6    7   19.8
> ggplot(BOD,aes(factor(Time),demand)) + geom_bar()
{% endhighlight %}

![](https://raw.githubusercontent.com/lixinyao/lixinyao.github.io/master/pictures/2015/barchart1.png)

从大到小排序，且加数字标签

{% highlight r %}
> ggplot(BOD,aes(reorder(factor(Time),-demand),demand))  + geom_bar(stat="identity") +
+   geom_text(aes(label=demand),vjust=1.5,color="white",size=7)
{% endhighlight %}

![](https://raw.githubusercontent.com/lixinyao/lixinyao.github.io/master/pictures/2015/barchart2.png)

## 按数量统计

以diamonds数据集为例，格式如下：

{% highlight r %}
> head(diamonds)
  carat       cut color clarity depth table price    x    y    z
1  0.23     Ideal     E     SI2  61.5    55   326 3.95 3.98 2.43
2  0.21   Premium     E     SI1  59.8    61   326 3.89 3.84 2.31
3  0.23      Good     E     VS1  56.9    65   327 4.05 4.07 2.31
4  0.29   Premium     I     VS2  62.4    58   334 4.20 4.23 2.63
5  0.31      Good     J     SI2  63.3    58   335 4.34 4.35 2.75
6  0.24 Very Good     J    VVS2  62.8    57   336 3.94 3.96 2.48
{% endhighlight %}

{% highlight r %}
> ggplot(diamonds,aes(x=cut)) + geom_bar(stat="bin")
{% endhighlight %}

![](https://raw.githubusercontent.com/lixinyao/lixinyao.github.io/master/pictures/2015/barchart3.png)

# 分组条形图

## 普通分组条形图

以cabbage_exp数据集为例

{% highlight r %}
> cabbage_exp
  Cultivar Date Weight        sd  n         se
1      c39  d16   3.18 0.9566144 10 0.30250803
2      c39  d20   2.80 0.2788867 10 0.08819171
3      c39  d21   2.74 0.9834181 10 0.31098410
4      c52  d16   2.26 0.4452215 10 0.14079141
5      c52  d20   3.11 0.7908505 10 0.25008887
6      c52  d21   1.47 0.2110819 10 0.06674995
{% endhighlight %}

{% highlight r %}
> ggplot(cabbage_exp,aes(Date,Weight,fill=Cultivar)) +
+   geom_bar(stat="identity",position="dodge") +
+   scale_fill_brewer(palette="Pastel1")
{% endhighlight %}

![](https://raw.githubusercontent.com/lixinyao/lixinyao.github.io/master/pictures/2015/barchart4.png)

## 正负分组条形图

设置一列pos为正负条形的位置

以csub数据集为例

{% highlight r %}
> csub = subset(climate,Source=="Berkeley"&Year>=1900)
> csub$pos = csub$Anomaly10y>=0
> head(csub)
      Source Year Anomaly1y Anomaly5y Anomaly10y Unc10y   pos
101 Berkeley 1900        NA        NA     -0.171  0.108 FALSE
102 Berkeley 1901        NA        NA     -0.162  0.109 FALSE
103 Berkeley 1902        NA        NA     -0.177  0.108 FALSE
104 Berkeley 1903        NA        NA     -0.199  0.104 FALSE
105 Berkeley 1904        NA        NA     -0.223  0.105 FALSE
106 Berkeley 1905        NA        NA     -0.241  0.107 FALSE
{% endhighlight %}

{% highlight r %}
ggplot(csub,aes(Year,Anomaly10y,fill=pos)) +
  geom_bar(stat="identity",position="identity")
{% endhighlight %}

![](https://raw.githubusercontent.com/lixinyao/lixinyao.github.io/master/pictures/2015/barchart7.png)

# 堆积条形图

还是以cabbge_exp数据集为例

{% highlight r %}
> cabbage_exp
  Cultivar Date Weight        sd  n         se
1      c39  d16   3.18 0.9566144 10 0.30250803
2      c39  d20   2.80 0.2788867 10 0.08819171
3      c39  d21   2.74 0.9834181 10 0.31098410
4      c52  d16   2.26 0.4452215 10 0.14079141
5      c52  d20   3.11 0.7908505 10 0.25008887
6      c52  d21   1.47 0.2110819 10 0.06674995
{% endhighlight %}

{% highlight r %}
> ggplot(cabbage_exp,aes(Date,Weight,fill=Cultivar)) +
+   geom_bar(stat="identity",position="stack",width=0.7) +
+   scale_fill_brewer(palette="Pastel1") +
+   guides(fill=guide_legend(reverse=TRUE))
{% endhighlight %}

![](https://raw.githubusercontent.com/lixinyao/lixinyao.github.io/master/pictures/2015/barchart5.png)

上个例子用`reverse=TRUE`来更改legend的顺序，现在用`order=desc`来解决

{% highlight r %}
> library(plyr)
> ggplot(cabbage_exp,aes(Date,Weight,fill=Cultivar,order=desc(Cultivar))) +
+   geom_bar(stat="identity",position="stack",width=0.7) +
+   scale_fill_brewer(palette="Pastel1")
{% endhighlight %}

![](https://raw.githubusercontent.com/lixinyao/lixinyao.github.io/master/pictures/2015/barchart6.png)

# 百分比堆积条形图

以ce数据集为例，增加百分比列

{% highlight r %}
> library(plyr)
> ce = ddply(cabbage_exp,"Date",transform,
+             percent_weight = Weight/sum(Weight) * 100)
> head(ce)
  Cultivar Date Weight        sd  n         se percent_weight
1      c39  d16   3.18 0.9566144 10 0.30250803       58.45588
2      c52  d16   2.26 0.4452215 10 0.14079141       41.54412
3      c39  d20   2.80 0.2788867 10 0.08819171       47.37733
4      c52  d20   3.11 0.7908505 10 0.25008887       52.62267
5      c39  d21   2.74 0.9834181 10 0.31098410       65.08314
6      c52  d21   1.47 0.2110819 10 0.06674995       34.91686
{% endhighlight %}

{% highlight r %}
ggplot(ce,aes(x=Date,y=percent_weight,fill=Cultivar)) +
  geom_bar(stat="identity")
{% endhighlight %}

![](https://raw.githubusercontent.com/lixinyao/lixinyao.github.io/master/pictures/2015/barchart8.png)

# 克利夫兰点图

Cleveland点图是条形图的变种，有时候比条形图更清晰，以tophit数据集为例

{% highlight r %}
> tophit = tophitters2001[1:25, ]
> tophit = tophit[, c("name","lg","avg")]
> nameorder = tophit$name[order(tophit$lg,tophit$avg)]
> tophit$name = factor(tophit$name,levels=nameorder)
> head(tophit)
            name lg    avg
1   Larry Walker NL 0.3501
2  Ichiro Suzuki AL 0.3497
3   Jason Giambi AL 0.3423
4 Roberto Alomar AL 0.3357
5    Todd Helton NL 0.3356
6    Moises Alou NL 0.3314
{% endhighlight %}

{% highlight r %}
> ggplot(tophit,aes(x=avg,y=name)) +
+   geom_segment(aes(yend=name),xend=0,colour="grey50") +
+   geom_point(size=3,aes(colour=lg)) +
+   scale_colour_brewer(palette="Set1",limits=c("NL","AL"),guide=FALSE) +
+   theme_bw() +
+   theme(panel.grid.major.y = element_blank()) +
+   facet_grid(lg ~ .,scales="free_y",space="free_y")
{% endhighlight %}

![](https://raw.githubusercontent.com/lixinyao/lixinyao.github.io/master/pictures/2015/barchart9.png)

# 小贴士

以后在小贴士这个栏目里，会发布几个平时不太注意到的函数或者是小方法，苦逼的生活就是*“哎呀，太TM累了，看会儿R手册歇会儿吧”*。

## example()函数

`example()`可以允许例子的代码，比如`example("seq")`,`example("ggplot2")`等

## help.search()函数

`help.search()`可以查自己不太确定的函数，比如想做个时间序列分析，可又忘了怎么做了，可以试着`help.search("time series analysis")`