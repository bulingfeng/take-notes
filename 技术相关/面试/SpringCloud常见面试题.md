## 微服务架构的优缺点

优点

1. 进行业务上的结构，每个微服务都负责一块单独的业务；这样能够让这个服务成为一个公共服务对外服务；
2. 可以方便的进行横向拓展从而可以提高并发量。
3. 可以方面的进行优雅的服务降级，比如流量高峰到来的时候可以把某些不重要的服务给停止掉来保证核心业务的正常运行

缺点

1. 对架构能力要求高，需要非常了解业务如何运作，然后才能合理的拆解出来符合业务的微服务
2. 要求编程水平的提高，比如分布式事务，分布式锁都要在开发人员掌握
3. 调试麻烦，以前可能是启动一个服务来今天调试可以了，现在可能需要启动7-8个服务才能进行调试

## 微服务怎么拆分

1. 高内聚低耦合，让每个服务指责单一，最好是能根据业务来进行拆分出来单独的独立的服务；
2. 在开发的过程中进行结构变化，也就是当业务沉淀下来和成熟的时候再进行拆分和结构

## 常用的微服务组件及其作用

1. 注册中心nacos
2. 服务调用openfeign
3. 配置中心 nacos
4. 熔断hystrix
5. 网关gateway
6. 链路追踪 skywalking或者zipkin

## 注册中心的核心功能原理是什么

服务注册：服务启动的时候会通过rest请求把自己注册到nacos的server上

服务端维护：nacos client端会为何一个和serer段的一个心跳，默认每5秒钟一次，当server超过15秒没有接受到某个服务的心跳就会把该服务的健康状态设置为false；如果超过30秒没有检查到该服务则会移除掉

clint端：nacos client端会有个定时任务，会去拉去nacos server的服务列表。

## 服务网关的作用

1. 鉴权
2. 白名单
3. 流量统计

## 什么是服务雪崩？什么是服务限流？

服务雪崩，就是某一个服务挂了，然后上面依赖的服务由于迟迟没有得到返回而造成线程的阻塞，从而导致自己也挂了。以此类推导致整个集群瘫痪。

## 什么是服务熔断？什么是服务降级？区别是什么？

服务熔断就是当A服务去依赖B服务的时候，B服务发生了异常从而避免导致A服务发生异常，这是一种自保的策略。

服务降级是根据业务的需求，通常是是流量高峰期的时候把某些功能给关闭掉来让核心业务流程不受影响，比如双11的时候，淘宝把退款的功能给取消掉，就是一种服务降级的表现。

## 说说seata的实现原理

第一阶段

发起事务的应用向TC发起一个事务注册，然后返回一个XID。

更新的时候会生成两个镜像，更新之前的镜像和更新之后的镜像。

第二阶段

通过XID和brandid来查询到对应的id，然后数据库数据进行对比，如果对比数据是一样的，则可以进行回滚。

二阶段提交：收到 TC 的分支提交请求，把请求放入一个异步任务的队列中，马上返回提交成功的结果给 TC。 然后异步的删除UNDO_LOG记录。

什么是二阶段提交

```
一阶段：业务数据和回滚日志记录在同一个本地事务中提交，释放本地锁和连接资源。

二阶段：

提交异步化，非常快速地完成。
回滚通过一阶段的回滚日志进行反向补偿。
```

## 说说ribbon有哪些负载均衡策略

1. 随机
2. 轮询
3. 在轮询的基础上加上重试
4. 权重
5. 寻找到并发最小的实例