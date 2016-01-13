---
layout: post
title: R语言与时间序列分析
categories: [R]
tags: [R]
---
简单介绍R时间序列分析的流程

# 原始时间序列

## 加载包

{% highlight r %}
library(TSA)
library(forecast)
library(reshape2)
library(zoo)
library(scales)
library(ggplot2)
library(plyr)
library(xlsx)
{% endhighlight %}

## 原始时间序列

{% highlight r %}
setwd("C:/Users/lixinyao_bj/Desktop/program/增值预估")
Mxianjin=read.xlsx("增值现金预估.xlsx",2,header=T,encoding="UTF-8",stringsAsFactors=F)
Mxianjin = na.omit(Mxianjin)
MXJts = ts(Mxianjin$现金,frequency=12,start=c(2009,1))
mydata = data.frame(M=index(MXJts),value=melt(MXJts)$value)
ggplot(mydata,aes(M,value)) + geom_line(size=1) + geom_point(size=3) + theme_bw()
{% endhighlight %}

![](https://raw.githubusercontent.com/lixinyao/lixinyao.github.io/master/pictures/2014/ts1.png)

# 平稳性

## 单位根检验

原假设：非平稳

{% highlight r %}
> adf.test(MXJts)
Augmented Dickey-Fuller Test
data:  MXJts
Dickey-Fuller = -4.3621, Lag order = 4, p-value = 0.01
alternative hypothesis: stationary
Warning message:
In adf.test(MXJts) : p-value smaller than printed p-value
{% endhighlight %}

## 差分

{% highlight r %}
MXJtsDiff = diff(MXJts)
mydata2 = data.frame(M=index(MXJtsDiff),value=melt(MXJtsDiff)$value)
{% endhighlight %}

### 一阶一次差分序列

{% highlight r %}
ggplot(mydata2,aes(M,value)) + geom_line(size=1) + geom_point(size=3) + theme_bw() + ylim(-2*10^8,2*10^8)
{% endhighlight %}

![](https://raw.githubusercontent.com/lixinyao/lixinyao.github.io/master/pictures/2014/ts2.png)

### 12阶差分

差分后效果很好

{% highlight r %}
MXJtsDiff2 = diff(MXJtsDiff,lag=12)
mydata3 = data.frame(M=index(MXJtsDiff2),value=melt(MXJtsDiff2)$value)
ggplot(mydata3,aes(M,value)) + geom_line(size=1) + geom_point(size=3) + theme_bw() + ylim(-2*10^8,2*10^8)
{% endhighlight %}

![](https://raw.githubusercontent.com/lixinyao/lixinyao.github.io/master/pictures/2014/ts3.png)

# 相关性

## ACF

{% highlight r %}
qacf = function(x, conf.level = 0.95) {
  ciline = qnorm((1 - conf.level)/2)/sqrt(length(x))
  bacf = acf(x,lag.max=30, plot = FALSE)
  bacfdf = with(bacf, data.frame(lag, acf))
  q = qplot(lag, acf, data = bacfdf, geom = "bar", stat = "identity",
             position = "identity", ylab = "ACF",width=0.2)
  q = q + geom_hline(yintercept = -ciline, color = "blue",
                      size = 1)
  q = q + geom_hline(yintercept = ciline, color = "blue",
                      size = 1)
  q = q + geom_hline(yintercept = 0, color = "red", size = 1)+theme_bw()
  return(q)
}
p = qacf(as.vector(MXJtsDiff2))
p
{% endhighlight %}

![](https://raw.githubusercontent.com/lixinyao/lixinyao.github.io/master/pictures/2014/ts4.png)

## PACF

{% highlight r %}
qpacf = function(x, conf.level = 0.95) {
  ciline = qnorm((1 - conf.level)/2)/sqrt(length(x))
  cacf = pacf(x,lag.max=30, plot = FALSE)
  cacfdf = with(cacf, data.frame(lag, acf))
  q = qplot(lag, acf, data = cacfdf, geom = "bar", stat = "identity",
             position = "identity", ylab = "PACF",width=0.2)
  q = q + geom_hline(yintercept = -ciline, color = "blue",
                      size = 1)
  q = q + geom_hline(yintercept = ciline, color = "blue",
                      size = 1)
  q = q + geom_hline(yintercept = 0, color = "red", size = 1)+theme_bw()
  return(q)
}
d = qpacf(as.vector(MXJtsDiff2))
d
{% endhighlight %}

![](https://raw.githubusercontent.com/lixinyao/lixinyao.github.io/master/pictures/2014/ts5.png)

# 定阶

## auto辅助定阶

	auto.arima(log(MXJts))

## 采用bestAIC来定阶

{% highlight r %}
get.best.arima = function(x.ts, maxord = c(1,1,1,1,1,1))
{
  best.aic = 1e8
  n = length(x.ts)
  for (p in 0:maxord[1]) for(d in 0:maxord[2]) for(q in 0:maxord[3])
    for (P in 0:maxord[4]) for(D in 0:maxord[5]) for(Q in 0:maxord[6])
    {
      fit = arima(x.ts, order = c(p,d,q),
                   seasonal = list(order=c(P,D,Q),frequency(x.ts)), method = "ML")
      fit.aic = -2 * fit$loglik + (log(n) + 1) * length(fit$coef)
      if (fit.aic < best.aic)
      {
        best.aic = fit.aic
        best.fit = fit
        best.model = c(p,d,q,P,D,Q)
      }
    }
  list(best.aic, best.fit, best.model)
}
best.arima.elec = get.best.arima(log(MXJts),maxord = c(1,1,1,1,1,1))
best.fit.elec = best.arima.elec[[2]]
bae = best.arima.elec [[3]]
{% endhighlight %}

结果中的值就是ARIMA(p,d,q,P,D,Q)

# 残差检验

{% highlight r %}
y=Arima(log(MXJts),order=c(bae[1],bae[2],bae[3]),seasonal=c(bae[4],bae[5],bae[6]),method="ML")
rstandard(y)
cacf = function(x, conf.level = 0.95) {
  ciline = qnorm((1 - conf.level)/2)/sqrt(length(x))
  bacf = acf(x,lag.max=30, plot = FALSE)
  bacfdf = with(bacf, data.frame(lag, acf))
  q = qplot(lag, acf, data = bacfdf, geom = "bar", stat = "identity",
             position = "identity", ylab = "SDEACF",width=0.2)
  q = q + geom_hline(yintercept = -ciline, color = "blue",
                      size = 1)
  q = q + geom_hline(yintercept = ciline, color = "blue",
                      size = 1)
  q = q + geom_hline(yintercept = 0, color = "red", size = 1)+theme_bw()
  return(q)
}
c = cacf(as.vector(rstandard(y)))
c
{% endhighlight %}

![](https://raw.githubusercontent.com/lixinyao/lixinyao.github.io/master/pictures/2014/ts6.png)

# 预测

{% highlight r %}
z = forecast(y,level=c(1,95))
z$fitted
z$residuals
z$mean
exp(z$mean)
exp(z$lower)
exp(z$upper)
yfit = forecast(y,level=c(1,35),h=5)
yfcast = forecast(y,h=5,level=c(1,35))
funggcast = function(dn, fit, fcast) {
  require(zoo)  #needed for the 'as.yearmon()' function
  require(plyr)
  # Extract Source and Training Data
  ds = as.data.frame(dn)
  names(ds) = "observed"
  ds$date = as.Date(time(dn))
  
  # Extract the Fitted Values (need to figure out how to grab confidence
  # intervals)
  dfit = as.data.frame(exp(yfit$fitted))
  dfit$date = as.Date(time(yfit$fitted))
  names(dfit)[1] = "fitted"
  
  ds = merge(ds, dfit, all.x = T)  #Merge fitted values with source and training data
  
  # Exract the Forecast values and confidence intervals
  dfcastn = exp(as.data.frame(yfcast))
  dfcastn$date = as.Date(as.yearmon(row.names(dfcastn)))
  names(dfcastn) = c("forecast", "lo1", "hi1", "lo35", "hi35", "date")
  
  pd <- rbind.fill(ds, dfcastn)  #final data.frame for use in ggplot
  return(pd)
}
pd = funggcast(MXJts,yfit,yfcast)
pd[(dim(pd)[1]-4):dim(pd)[1],1] = as.Date(c("2015-07-01","2015-08-01","2015-09-01","2015-10-01","2015-11-01"))
pd[78,4] = pd[78,2]
p1a = ggplot(data = pd,aes(x = date,y = observed)) 
p1a = p1a + geom_line(aes(y=fitted),col="firebrick",size=1) + geom_line(col="steelblue",size=1)
p1a + geom_ribbon(aes(ymin=lo35,ymax=hi35),fill="pink") + geom_line(aes(y=forecast),col="black",size=1) + ylab("总现金")+theme_bw()
{% endhighlight %}

![](https://raw.githubusercontent.com/lixinyao/lixinyao.github.io/master/pictures/2014/ts7.png)

