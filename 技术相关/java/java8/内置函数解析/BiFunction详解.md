## 简述

上次分析完了Function这个内置接口，本次来分析BitFunction这个接口；Bi经过查询是Binary的缩写，汉语意思是`二进制的,二元的`。在这个语境中是二元的含义。也就是有两个参数的意思（既然有二元的那么是不是还有1元的呢？答案是有的）

## 源码分析

```java
@FunctionalInterface
public interface BiFunction<T, U, R> {

    R apply(T t, U u);

    default <V> BiFunction<T, U, V> andThen(Function<? super R, ? extends V> after) {
        Objects.requireNonNull(after);
        return (T t, U u) -> after.apply(apply(t, u));
    }
}
```

从源代码来分析，其实套路和Function接口差不多的套路。但是相对于Function接口，BitFunction接口中没有compose这个方法。

从源码整体上分析来看，就是入参变成了2个。

`apply`方法暂时先不分析了，因为这个方法太简单了。

## andThen

这个方法需要注意的是入参是`Function<? super R, ? extends V> afte`,这个里面是**Function**接口，并不是**BiFunction**接口哦。这里需要注意。

并且还加了一个自定义范型`V`，这样能够拓展返回参数的范围。

```java
public class BiFunctionAndThenExample {
    public static void main(String[] args) {
        // 定义一个 BiFunction，计算两个整数的和
        BiFunction<Integer, Integer, Integer> addFunction = (a, b) -> a + b;

        // 定义一个 Function，将一个整数乘以系数
        Function<Integer, Integer> multiplyFunction = x -> x * 2;

        // 使用 andThen 方法将两个函数组合
        BiFunction<Integer, Integer, Integer> composedFunction = addFunction.andThen(multiplyFunction);

        int result = composedFunction.apply(3, 4); // 先计算和（7），然后乘以系数（14）
        System.out.println("Result: " + result); // 输出：Result: 14
    }
}
```

