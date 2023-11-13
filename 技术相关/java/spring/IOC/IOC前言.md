## 前言

现在是 2023 年了，突然发现自己包括周围的同事还是都是在编写业务代码，而对真正的底层代码关注的是越来越少了。

技术界发展很快，所以一些开发人员每天都在追一些所谓的新技术，昨天使用 docker了，今天又开始使用k8s云部署了，而从来不去关注最核心的问题：

> 代码质量和代码底层如何实现的问题。

我在这里并不是说关注新技术不好，我只是想说，2023 年了如果你还是停留在单纯的会使用某个工具或者框架上，那么你将在未来将会越来越没有竞争力。

因为对于如何使用，大不了买1 门课，花个 1周时间学习一波，我相信只要是能看完视频的基本上都会使用了。

言归正传，Spring 有两大核心IOC和AOP，那么我下面会尝试来写点水文来讲述下整个Spring框架，那么讲整个 Spring 框架之前，先从IOC来开刀；

> 未来的文章都是自己的理解，大家要保持辩证的心态和一切以源码为准的心态来看我的讲述，因为我的讲述只是我的理解，如果有问题欢迎指出来。

未来的分析我将会借助**ChatGpt 和Spring 的官网**来进行分析；

[!spring 官网](https://docs.spring.io/spring-framework/reference/overview.html)

所有的分析都是基于Spring6.0和Java 17。

最后引用下官方对Spring的描述：

> If you are just getting started with Spring, you may want to begin using the Spring Framework by creating a [Spring Boot](https://projects.spring.io/spring-boot/)-based application. Spring Boot provides a quick (and opinionated) way to create a production-ready Spring-based application. It is based on the Spring Framework, favors convention over configuration, and is designed to get you up and running as quickly as possible.
>
> 如果你刚刚开始使用Spring，你可能希望通过创建一个基于Spring Boot的应用程序来开始使用Spring框架。Spring Boot 提供了一种快速（且固执己见）的方式来创建生产就绪的基于 Spring 的应用程序。**它基于 Spring 框架，优先于约定而不是配置，旨在让您尽快启动和运行。**