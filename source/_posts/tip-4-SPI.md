---
title: tip-4-SPI
date: 2019-04-28 19:27:23
categories:
tags:
---


# <center>SPI---Service Provider Interfaces</center>

## 什么是JAVA SPI?

&nbsp;&nbsp;SPI全称 **Service Provider Interfaces**

&nbsp;&nbsp;Java SPI定义了四个主要组件：

1. Service 服务

&nbsp;&nbsp;**一系列接口或类**提供明确的功能来给外部的访问和调用

2. Service Provider Interface 服务提供接口

&nbsp;&nbsp;做为服务的代理或者对外发布的服务的**一个接口或抽象类**。服务和SPI共同组成了JAVA生态圈的API。

&nbsp;&nbsp;[SPI和API的区别](https://stackoverflow.com/questions/2954372/difference-between-spi-and-api)：

API:给其他服务调用的接口。

SPI:定义了接口的功能，给服务实现者去实现具体接口功能的接口。




3. Service Provider 服务提供

&nbsp;&nbsp;SPI的具体实现者。通过resouce文件夹下META-INF/services下配置。用对应SPI的全类名路径作为文件名，文件内容则是SPI的具体实现的全类名路径。


&nbsp;&nbsp;可以将服务提者以jar文件的形式放在应用的classpath下，java 扩展类路径下，或者我们自定义的类路径中。


4. ServiceLoader 服务加载

&nbsp;&nbsp;SPI的核心就是ServiceLoader。用于将服务提供者加载到内存中来。


## SPI有什么用？

discovering and loading implementations matching a given interface

发现和加载符合给定接口的实现。利用这个特性，能让java更具有扩展性。


## [JAVA SPI怎么使用](https://www.baeldung.com/java-spi)？


1. 定义我们的服务提供者接口，他提供了一个功能。


```
public interface SpiProviderInterface {
	String fun( );
}
```

2.ServiceLoader（核心）

```
public class MyLoader {
 
    static ServiceLoader<ExchangeRateProvider> loader = ServiceLoader
      .load(ExchangeRateProvider.class);
  
    public static  Iterator<ExchangeRateProvider> providers(boolean refresh) {
        if (refresh) {
            loader.reload();
        }
        return loader.iterator();
    }
}

```


3.服务提供者

- 创建我们的服务提供者

```
public class MyProvider 
  implements SpiProviderInterface {
  
    @Override
    public String fun( ); {
        return "myFun";
    }
}
```

- 进行标识没让ServiceLoader扫描到

在META-INF/services/下创建xxx.SpiProviderInterface文件，内容为XXX.MyProvider


我们可以提供多个jar包的形式来实现我们的SPI，然后在调用MyLoader.providers方法时，会将所有实现进行加载和返回。以达到动态扩展的目的





参考文献：
[SPI](https://www.baeldung.com/java-spi)

[SPI和API的区别](https://stackoverflow.com/questions/2954372/difference-between-spi-and-api)
