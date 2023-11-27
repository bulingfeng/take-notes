# SpringBoot官方文档

## 2023-11-21

发现一个老外的网站用来学习springboot等方面的知识，链接如下

> https://www.baeldung.com/spring-boot

今天上班读了些spring的官方文档，发现还是有很多的收获点的，所以打算把整个spring的文档按照目录都给大致的读一遍，从而让自己关于springboot的知识更加的系统化。

下面这个文档是官方详细讲解关于SpringBoot细节的文档，入口是message-convert(虽然我不知道它为啥放这里)

> https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#web.servlet.spring-mvc.message-converters

### SpringBoot相关知识的记录

#### 1.相关错误日志排查

自己可以通过在yaml文件中设置日志的级别为debug

```yaml
logging:
  level:
    root: info
```

当然如果不想改变springboot的配置文件的日志结构，只想查看具体的错误出在哪里，可以在命令行来做要给debug命令操作

```shell
$ java -jar myproject-0.0.1-SNAPSHOT.jar --debug
```

这样就能输出对应的debug日志，方面开发人员进行排查问题

#### 2.关于Lazy的懒加载



### 英语学习

1. so that you have a good grounding of the basics.