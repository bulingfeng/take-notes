# Consumer相关接口详解

## 简介

从Consumer这个名称来看就知道这个接口是一个消费类的接口。

## Consumer源码分析

```java
@FunctionalInterface
public interface Consumer<T> {

    void accept(T t);

    default Consumer<T> andThen(Consumer<? super T> after) {
        Objects.requireNonNull(after);
        return (T t) -> { accept(t); after.accept(t); };
    }
}
```

这个源码就比较简单了。我们直接来写个例子来结尾把。

```java
public class ConsumerExample {
    public static void main(String[] args) {
        Consumer<String> printUpperCase = (str) -> System.out.println(str.toUpperCase());

        printUpperCase.accept("hello"); // 输出：HELLO
    }
}

```

## IntConsumer源码分析

```java
@FunctionalInterface
public interface IntConsumer {

    void accept(int value);

    default IntConsumer andThen(IntConsumer after) {
        Objects.requireNonNull(after);
        return (int t) -> { accept(t); after.accept(t); };
    }
}
```

IntConsumer和Consumer的用法完全一模一样。只不过接受的参数只能为int类型。有人说为何不适用`Consumer<Integer>`来做呢？

使用Consumer`<Integer>`也能做，但是涉及到拆箱和装箱，从而会影响到效率。所以有了IntConsumer这个接口。

## 总结

java8以后的以后接口编程中，有很多Intxxx之类的接口，比如IntFunction，这个接口的意思也是一样的，输入的参数只接受int值。

所以看到Intxxx之类的接口，都代表的是参数只能接受int类型的值（如果一个函数只有返回值，那么Intxxx代表的是返回值是int，比如IntSupplier）。

