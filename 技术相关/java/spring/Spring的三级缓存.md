---
title: "Spring的三级缓存"
subtitle: "Spring的三级缓存"
layout: post
author: "bulingfeng"
header-style: text
tags:
- spring
---

## 简介

Spring中的对象的创建流程如下：

> 1. 创建Aservice普通对象
> 2. 注入bService -> 去单例池中寻找bService对象 ->创建BService对象
> 3. 填充其他属性
> 4. 其他操作
> 5. 初始化后(AOP) ---> AService代理对象 这个时候放入单例池的对象是AService的代理对象，而不是AService的对象本身
> 6. 放入单例池

而如果BService中需要注入AService对象呢？那么也会走项目的流程，于是在第二步就发生了循环依赖。

但是实际这么写，Spring也不会报错，也是照样可以运行，那么什么情况下才会发生循环依赖呢？带着问题看下面问题。

三级缓存名字

- 单例池 singletonObjects
- earlysingletonObject
- 第三级缓存

参考文章

```
https://developer.aliyun.com/article/766880
```

```
https://youngff.github.io/MyHearthStone/spring%20framework/spring-core/spring-cycle-depend/
```

