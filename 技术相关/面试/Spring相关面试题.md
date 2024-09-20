## 说说spring的循环依赖，已经三级缓存

https://juejin.cn/post/7099745254743474212

## Spring中的Bean的生命周期有哪些步骤

1. 构造方法推断；就是bean中药使用哪个构造方法来进行构建对象；
2. 实例化；
3. 依赖注入
4. 执行postConstruct等初始化之前的方法
5. 初始化InilazizingBean
6. 初始化完成执行aop
7. 容器关闭对bean进行销毁

## Spring事务是如何工作的





## ApplicationContext和beanfactory有什么区别

1. ApplicationContext是beanfacotry的一个继承接口，拓展了更多的功能；比如国际化，获取环境变量等功能。



## BeanFactory和FactoryBean的区别

https://refblogs.com/article/464

## SpringAop的实现

SpringAop是ioc的一个拓展点，是通过BeanPostprocessord的后置方法来进行实现的；

通过jdk动态代理或者cglib来创建代理对象，然后调用的时候会调用到字节码文件中DynamicAdvisorinterceptor类中的intercept方法；

## Spring的事务是如何回滚的

spring的事务是由aop来实现的，首选要生成具体的代理对象，然后按照aop的标准流程执行；

首先要获取链接，然后执行sql，如果执行成功则提交事务，如果失败