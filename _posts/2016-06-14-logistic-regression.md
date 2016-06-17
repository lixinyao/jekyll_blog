---
layout: post
title: R语言与二分类logistic回归
categories: [R]
tags:
- R
- logistic回归
- 逻辑回归
- 机器学习
- 统计
---

# 二分类logistic回归介绍

逻辑回归是用来解决分类问题的统计或机器学习模型，这里我们只介绍最简单的 **而分类** 。常见的应用有识别垃圾邮件、判断病人是否有某种疾病、预测客户是否买房等等。

## logistic函数和logit变换

现在普及两个基础函数：

1. logistic函数。$f(x)=\frac{1}{1+e^{-x}}$
2. logit变换。$logit P=ln(\frac{P}{1-P})$

有趣的是，**logit变换是logistic函数的反函数** ，$f(x)=\frac{1}{1+e^{-x}}$ , $g(x)=ln(\frac{P}{1-P})$, $g(f(x))=ln(\frac{\frac{1}{1+e^{-x}}}{1-\frac{1}{1+e^{-x}}})=x$

```r
library(ggplot2)
logistic_fun = function(x){1/(1+exp(-x))}
ggplot(data.frame(x = c(-8,8)),aes(x)) +
  stat_function(fun = logistic_fun,
                color = "blue",
                size = 1)
```

logistic函数图形如下：

![](https://raw.githubusercontent.com/lixinyao/lixinyao.github.io/master/pictures/2016/logistic_fun.png)

## 二分类logistic回归详解

比如要解决二分类问题，我们最先想到的是建立个线性回归方程 $y_{i}=\beta_{0}+\beta_{1}x_{1}+\beta_{2}x_{2}+...+\beta_{n}x_{n}$ 。但是这个回归方程 $y_{i}$ 的取值范围不受约束，而 $y_{i}$在分类问题中应该只有 **1** 和 **0** 两个取值，自然想到将它代入logistic函数，得 $f(y_{i})=\frac{1}{1+e^{-y_{i}}}$ ，$f(y_{i})$ 取值在0～1之间。

这样的话，我们的 **logistic回归方程** 就是

$$y_{i}=\frac{1}{1+e^{-(x_{0}+x_{1}+...+x_{n})}}$$

有了回归方程后就可以用 **极大似然估计进行参数估计** 。假设有n个独立的训练样本，$(x_{1},y_{1}),(x_{2},y_{2})...(x_{n},y_{n})$ , $y\in (0,1)$

设 $p_{i}=P(y_{i}=1\mid x_{i})$ 是 $y_{i}=1$ 的概率，那么 $y_{i}=0$ 的概率就是 $1-p_{i}$

任意一个观测值 $(x_{i},y_{i})$ 出现的概率是

$$P(x_{i},y_{i})=p_{i}^{y_{i}}(1-p_{i})^{1-y_{i}}$$

容易构造 **似然函数**

$$L(w)=\prod_{i=1}^{m}P(x_{i},y_{i})$$

取对数即得到 **对数似然函数**

$$lnL(w)=\sum_{i=1}^{m}[y_{i}lnp_{i}+(1-y_{i})ln(1-p_{i})]$$

接下来的问题就是最大化似然函数来求解参数。这里就要用到一些经典的 **数值优化算法**
