## SpringBoot自动配置原理

1. 通过@SpringBootConfiguration引入@EnableAutoConfiguration注解
2. @EnableAutoConfiguration注解来引入@Import注解，然后import注解使用AutoConfigurationImportSelector来读取spring.factories种配置的类，然后去重和校验是否有效，把那些类注入到IOC中。

## SpringBoot的启动原理

```java
return run(new Class<?>[] { primarySource }, args);
```

1. 进行初始化操作来来做一些操作，比如判断webApplicationType的类型，也就是容器的类型是webflux还是servlet；比如加载一些监听器等；监听器作用就是做完某些操作以后做的一些操作，比如刷新完上下文之后，而触发的一些动作。
2. 调用refresh方法来把自动配置类的东西加载到ioc容器当中
3. refresh中模版方法来来实现很多功能，包括自动配置的加载

## SpringBoot内置tomcat启动原理

1. SpringApplication.run运行的时候就要createApplicationContext来创建容器。
2. 然后就会调用refresh方法来把bean加载到ioc容器当中

