## 谈谈Mybatis的启动过程

1. 加载Mybatis的配置文件，包括数据库连接信息和映射文件。
2. 获取到SqlSessionFactory对象，SqlSessionFactory的核心对应，用于生成SqlSession对象。
3. SqlSession来执行具体的sql，用完之后进行关闭。
4. 如果是通过Mapper的形式的话，在启动的过程中mybatis根据映射来动态生成mapper的实现类。启动完成sqlsession来执行数据库操作了

三个重要的组件：SqlSessionFactory，SqlSession，Excutor（执行器）。

## Mybatis缓存的设计

缓存的作用

mybatis为了提高访问数据库的性能，它将查询的结果放到缓存当中，每次当有相同查询请求的时候，会直接从缓存中拿数据，避免再次访问数据库，从而提高了响应速度。

一级缓存

mybatis默认是开启的，并且是sqlsession级别的，当sqlsession执行同一个sql的查询的时候会直接走内存而不会直接查询数据库

二级缓存

二级缓存是Mapper级别的，默认是关闭的；当不同的sqlsession执行相同的sql的时候，如果是开启了二级缓存，那么会去查找二级缓存，如果查找到直接返回客户端。

问题是当两个服务器上一个执行查找，一个执行更新，当更新之后，查找并不能查到到二级缓存。

配置文件中需要开启二级缓存 name=cacheEnable  useCache=true

使用的顺序是先走二级缓存 再走一级缓存，最后走数据库

三级缓存

三级缓存默认是不支持的，但是自己可以自己去实现Cache接口来实现

## Mybatis一级缓存导致的问题

https://juejin.cn/post/7314365539168993289

## SqlSession的数据安全问题

sqlsession为什么是线程不安全的

1. sqlsession底层是基于jdbc的connection对象，connection对象就是线程不安全的，因此sqlsession也是线程不安全的
2. sqlsession中中包含了很多数据库连接和事务相关操作，如果多个线程是访问可能会导致事务错乱
3. mybatis的一级缓存也是基于sqlsession的，如果多个线程访问sqlsession的话会导致数据的混乱或者数据不一致

解决

每个线程都创建一个新的sqlsession；访问sqlsession的时候加锁来保证sqlsession线程安全；使用threadlocal来让每个线程分配一个sqlsession。

## 介绍Mybatis的工作原理

从两方面来介绍

1. mybatis的初始化过程
2. 处理SQL的流程

处理sql的流程

> 通过sqlsession的getMapper方法来获取到带来对象，然后代理对象调用sql的方法，从而拿到sql语句以及赋值。

## 谈谈对Mybatis的执行器的理解

Executor有三类

1. simple；每次都是创建一个新的statement；
2. reuse；根据二级缓存来判断是否复用statement
3. batch；批量处理

