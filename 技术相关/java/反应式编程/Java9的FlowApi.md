# Java9的Flow API

## 什么是反应式编程

> 反应式编程是一种利用反应式流的编程技术。而反应式流是以异步方式处理潜在无边界数据流的标准技术，其处理时按先后次序进行，并且带有不可阻塞的背压。
>
> 背压是发表–订阅模式下的一种常见的流量控制机制，目的是避免流中事件处理的消费者由于处理速度较慢，被一个或多个快速的生产者压垮。

背压

> 我的理解就是承受到来自外部的压力，好像有东西压在背上一样。根据以下这句话更好理解：
>
> > 执行同步调用时，系统默认就会收到来自阻塞API的背压。遭遇这种不幸的场景时，你将无法执行任何任务，直到阻塞操作完成。
>
> 与之相反，使用异步式API，硬件资源的使用率能够大幅提高，甚至达到其极限，不过你可能由此压垮下游处理速度较慢的组件。引入背压或者流量控制机制的目的就是解决这一问题，它们提供了一种协议，可以在不阻塞线程的情况下，避免数据接收方被压垮。

## Java9的Flow类

- 发布者（`Publisher` ）；
- 订阅者（`Subscriber` ）；
- 订阅（`Subscription` ）；
- 处理者（`Processor` ）。

这些接口直接的关系

> 凭借`Flow` 类，相互关联的接口或者静态方法可以构造流控（flow-controlled）组件。
>
> Publisher生产的元素可以被一个或多个`Subscriber` 消费，`Publisher` 与`Subscriber` 之间的关系通过`Subscription` 管理。`Publisher` 是顺序事件的提供者，并且这些事件的数量可能没有上限，不过它也受背压机制的制约，按照`Subscriber` 的反馈进行元素的生产。`Publisher` 是一个Java函数式接口（它仅仅声明了一个抽象方法），`Subscriber` 可以把自己注册为该事件的监听方从而完成对`Publisher` 事件的注册。流量控制，包括`Publisher` 与`Subscriber` 之间的背压都是由`Subscription` 管理的.

Subscriber中的方法执行顺序如下：

> onSubscribe onNext* (onError | onComplete)?

## 下面来实现一个Flow的流式处理

```java
public class TempInfo {

    public static final Random random = new Random();

    private final String town;
    private final int temp;

    public TempInfo(String town, int temp) {
        this.town = town;
        this.temp = temp;
    }

    public static TempInfo fetch(String town) {      // ←---- 城市的TempInfo实例都通过静态工厂方法创建
        if (random.nextInt(10) == 0)       //←---- 获取当前温度，每十次获取操作可能随机失败一次
        throw new RuntimeException("Error!");
        return new TempInfo(town, random.nextInt(100));      // ←---- 返回温度，其值是介于华氏0度到99度之间的一个随机数
    }

    @Override
    public String toString() {
        return town + " : " + temp;
    }

    public int getTemp() {
        return temp;
    }

    public String getTown() {
        return town;
    }
}
```

```java
public class TempSubscriber implements Subscriber<TempInfo> {

    private Subscription subscription;

    @Override
    public void onSubscribe( Subscription subscription ) {       //←---- 保存Subscription并发送第一个请求
        this.subscription = subscription;
        subscription.request( 1 );
    }

    @Override
    public void onNext( TempInfo tempInfo ) {      // ←---- 打印输出接收到的温度数据并发送下一个数据请求
        System.out.println( tempInfo );
        subscription.request( 1 );
    }

    @Override
    public void onError( Throwable t ) {       //←---- 发生错误时，打印出错信息
        System.err.println(t.getMessage());
    }

    @Override
    public void onComplete() {
        System.out.println("Done!");
    }
}
```

```java
public class TempSubscription implements Flow.Subscription {

    private final Flow.Subscriber<? super TempInfo> subscriber;
    private final String town;

    public TempSubscription( Flow.Subscriber<? super TempInfo> subscriber,
                             String town ) {
        this.subscriber = subscriber;
        this.town = town;
    }

    @Override
    public void request( long n ) {
        for (long i = 0L; i < n; i++) {       //←---- Subscriber每处理一个请求执行一次循环
            try {
                subscriber.onNext( TempInfo.fetch( town ) );       //←---- 将当前温度发送给Subscriber
            } catch (Exception e) {
                subscriber.onError( e );      // ←---- 查询温度时如果发送失效，将出错信息返回给Subscriber
                break;
            }
        }
    }

    @Override
    public void cancel() {
        subscriber.onComplete();       //←---- 如果Subscription被取消了，那么向Subscriber发送一个完成（onComplete）信号
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        getTemperatures("New York").subscribe(new TempSubscriber());  //←---- 创建一个新的纽约温度的Publisher，并向其订阅TempSubscriber 事件
    }

    private static Publisher<TempInfo> getTemperatures(String town) {   //←---- 向注册了该事件的Subscriber返回一个发送TempSubscription的Publisher对象
        return subscriber -> subscriber.onSubscribe(
                new TempSubscription(subscriber, town)
        );
    }
}
```

> 我们开发的这个程序目前存在一个微妙的缺陷。不过，由于温度数据所构成的Stream会被`TempInfo` 工厂方法随机抛出的异常中断，这个问题被隐藏了。如果注释掉随机生成错误的那段代码，让程序持续运行足够长的时间，你猜猜会发生什么情况？
>
> **答案** ：这段代码的问题在于每次`TempSubscriber` 接受一个新的元素都会调用它的`onNext` 方法，`onNext` 方法又会向`TempSubscription` 发送一个新请求，接着`request` 方法又会向`TempSubscriber` 发送另一个元素。这种递归的调用一个接着一个被压入栈，最终导致栈溢出，造成像下面这样的`StackOverflowError` 错误：

修复方案

```java
public class TempSubscription implements Subscription {       ←---- 为了节省页面，刻意省略了原TempSubscription类中未改动的代码

    private static final ExecutorService executor =
                                       Executors.newSingleThreadExecutor();

    @Override
    public void request( long n ) {
        executor.submit( () -> {       ←---- 另起一个线程向subscriber发送下一个元素
            for (long i = 0L; i < n; i++) {
                try {
                    subscriber.onNext( TempInfo.fetch( town ) );
                } catch (Exception e) {
                    subscriber.onError( e );
                    break;
               }
            }
        });
    }
}
```

