# Spring Cloud Alibaba

> 阿里巴巴出的微服务架构解决方案





## 三层架构 + MVC

​		架构 -> 就是为了解耦

有了三层架构还不够，我们还需要一些框架来帮助我们，简化我们的开发



## 开发框架

Spring

> Spring 提供了两大特性，IOC 和 AOP
>
> Spring 是一个轻量级的 JavaEE 开源框架，主要作用是为了解决企业级开发的复杂度问题，复杂度 -> 耦合度
>
> Spring 容器
>
> 但和其它语言（Ruby、Scala）相比一样不方便，不够开箱即用，需要大量配置

Spring Boot

> 新一代 JavaEE 开发标准，拥有开箱即用的特性



## 微服务架构

> 更好的进行分布式系统开发
>
> 就是拆分单体应用，将一个应用拆分成多个服务，每个服务都是一个可以独立运行的项目



## 再谈微服务

> 分布式系统开发一定会遇到的这四个问题
>
> 1、这么多服务，客户端如何访问
>
> 2、这么多服务，服务之间如何通信
>
> 3、这么多服务，如何治理
>
> 4、服务挂了，怎么办？



## 解决方案

> Spring Cloud ，是一个生态，是为了解决微服务架构遇到的问题
>
> 想要使用 Spring Cloud 必须基于 Spring Boot
>
> 
>
> 1、Spring Cloud Netflix （过时）
>
> Netflix 是国际上非常有影响力的多媒体，在线服务提供商，号称全球最大
>
> Netflix 提供了非常多的微服务解决方案
>
> ​	客户端如何访问						-> API 网关，Zuul 组件
>
> ​	如何治理								   -> 服务注册与发现，Eureka
>
> ​	服务之间如何通信					-> Feign 客户端，基于Http Client，所以是 HTTP 的通信方式，同步并阻塞
>
> ​	服务挂了								   -> 熔断机制 Hystrix
>
> 但 Netflix 在 2018 年 12 月 12 日，宣布 Spring Cloud Netflix 系列技术栈进入维护模式
>
> 
>
> 2、Apache Dubbo Zookeeper(2.6.x)
>
> ​	服务之间如何通信					-> Dubbo 是一个高性能的 Java RPC 通信框架
>
> ​	如何治理									-> Zookeeper 是一个分布式协调系统，能解决服务注册与发现
>
> ​	客户端如何访问						-> API网关，没有，再找第三方或者自己实现
>
> ​	服务挂了									-> Hystrix
>
> 
>
> 3、Spring Cloud Alibaba
>
> Spring Cloud Alibaba 致力于提供微服务开发的一站式解决方案。此项目包含开发分布式应用微服务的必需组件，方便开发者通过 Spring Cloud 编程模型轻松使用这些组件来开发分布式应用服务。
>
> 依托 Spring Cloud Alibaba，您只需要添加一些注解和少量配置，就可以将 Spring Cloud 应用接入阿里分布式应用解决方案，通过阿里中间件来迅速搭建分布式应用系统。



## Service Mesh 服务网格

> 因为微服务架构还不够成熟，所以在 2018 年提供了新的微服务架构，号称下一代微服务架构标准
>
>  Istio 可能需要掌握的一套微服务解决方案



## 万变不离其宗，一通百通

1、API 网关，服务路由

2、HTTP、RPC、异步调用

3、服务注册与发现 -> 高可用

4、熔断、限流、服务降级

根本原因是，网络是不可靠的