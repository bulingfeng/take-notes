下面是简单一个类实现一个接口，然后使用@Service来修饰一个类，结果发现报错：

```tex
No qualifying bean of type 'org.example.service.BService' available: expected single matching bean but found 2: BServiceImpl,BService
```

代码如下：

```java
public interface BService {
    void insertB();
}
```

```java
@Service
public class BServiceImpl implements BService {

    @Autowired
    private TableBMapper tableBMapper;


    @Transactional
    @Override
    public void insertB() {
        TableB b=new TableB();
        b.setName("insertB");
        tableBMapper.insert(b);
        throw new RuntimeException("insertB异常");
    }
}
```

测试代码

```java
		@Test
    public void differentClassTest(){
        aService.insertA();
    }
```

作为每天都在curd的程序员，这样的代码是每天写的。而且重点是：

> BService接口只有BServiceImpl一个实现类。所以不可能出现两个同样的BService类型的类；
>
> 而且根据报错信息，是BService和BServiceImpl两个类型的类注入到了IOC容器中；
>
> > 一般来说，BService这个接口是不会注入到容器中的；

## 问题

最后发现是 mybatis 填写扫描包的时候写的有问题，应该具体到某个 mapper 下面而不能写整个目录；

错误的扫描包：

```java
@MapperScan("org.example")
```

这个扫描的范围太大，从而导致BService也被注入到了IOC容器中；

正确的方式，只扫描 mapper 的包

```java
@MapperScan("org.example.mapper")
```

