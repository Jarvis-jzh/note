# Apache Dubbo Zookeeper

Spring Boot + Spring Cloud 组件多，功能完备

Spring Boot + Dubbo + Zookeeper 组件少，功能非完备



## 微服务四大问题

最大的问题 -> 网络是不可靠的

- 客户端如何访问这么多的服务
  - API 网关
- 服务与服务之间如何通信
  - 同步通信
    - HTTP(Apache Http Client)
    - RPC(Dubbo 只支持 Java，Apache Thrift，gRPC)
  - 异步通信
    - 消息队列（kafka	RabbitMQ	RocketMQ）
- 这么多服务，如何管理
  - 服务治理 
    - 服务注册与发现
      - 基于客户端的服务注册与发现
        - Apache Zookeeper
      - 基于服务端的服务注册与发现
        - Netflix  Eureka
- 服务挂了，怎么办
  - 重试机制
  - 服务熔断
  - 服务降级
  - 服务限流



