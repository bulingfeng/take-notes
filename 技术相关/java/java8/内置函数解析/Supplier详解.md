# Supplier详解

supplier翻译成中文是提供的意思，这个函数式方法顾名思义就是一个提供值的一个方法。

## 源码

```java
@FunctionalInterface
public interface Supplier<T> {

    /**
     * Gets a result.
     *
     * @return a result
     */
    T get();
}
```

这个函数应该是最简单的一个，可以返回你想要返回的值的类型。