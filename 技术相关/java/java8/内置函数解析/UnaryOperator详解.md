## 简介

介绍完了二元的Function，也就是BiFunction这个接口，那么下面来介绍一个一元的Function。这个接口名字叫**UnaryFunction**。从名字我们就知道，它是输入一个参数，然后返回一个参数的接口。

## 源码解释

```java
@FunctionalInterface
public interface UnaryOperator<T> extends Function<T, T> {

    /**
     * Returns a unary operator that always returns its input argument.
     *
     * @param <T> the type of the input and output of the operator
     * @return a unary operator that always returns its input argument
     */
    static <T> UnaryOperator<T> identity() {
        return t -> t;
    }
}
```

这个接口就是一个方法，其实就是你输入一个参数，然后原封不动的把这个参数给返回来即可（传入的参数和返回的参数类型是一样的）。其实和Function中的identify方法是效果是一样的，Function中identify函数如下：

```java
static <T> Function<T, T> identity() {
        return t -> t;
    }
```

硬要说两者中的identify不一样的地方，那么就是UnaryFunction接口中的identify函数返回参数少写了一个那么一丢丢代码。