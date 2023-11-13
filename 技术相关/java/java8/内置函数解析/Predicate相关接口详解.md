## 简介

predicate是一个输入任何值，但是返回是一个boolean的一个接口。

使用所有的函数式编程，例如Function,Predicate等等，都需要注意一点就是，到最后一定是调用其中的最核心方法来返回自己想要的结果。比如Function是调用apply方法，Predicate是调用test方法，Consumer接口是调用accept方法。

## 源码分析

```java
@FunctionalInterface
public interface Predicate<T> {

    boolean test(T t);

    default Predicate<T> and(Predicate<? super T> other) {
        Objects.requireNonNull(other);
        return (t) -> test(t) && other.test(t);
    }


    default Predicate<T> negate() {
        return (t) -> !test(t);
    }

    
    default Predicate<T> or(Predicate<? super T> other) {
        Objects.requireNonNull(other);
        return (t) -> test(t) || other.test(t);
    }

    /**
     * Returns a predicate that tests if two arguments are equal according
     * to {@link Objects#equals(Object, Object)}.
     *
     * @param <T> the type of arguments to the predicate
     * @param targetRef the object reference with which to compare for equality,
     *               which may be {@code null}
     * @return a predicate that tests if two arguments are equal according
     * to {@link Objects#equals(Object, Object)}
     */
    static <T> Predicate<T> isEqual(Object targetRef) {
        return (null == targetRef)
                ? Objects::isNull
                : object -> targetRef.equals(object);
    }

    /**
     * Returns a predicate that is the negation of the supplied predicate.
     * This is accomplished by returning result of the calling
     * {@code target.negate()}.
     *
     * @param <T>     the type of arguments to the specified predicate
     * @param target  predicate to negate
     *
     * @return a predicate that negates the results of the supplied
     *         predicate
     *
     * @throws NullPointerException if target is null
     *
     * @since 11
     */
    @SuppressWarnings("unchecked")
    static <T> Predicate<T> not(Predicate<? super T> target) {
        Objects.requireNonNull(target);
        return (Predicate<T>)target.negate();
    }
}
```

## test

这个方法无需介绍，这个是Predicate借口中最重要的方法。也就是输入一个值，然后返回一个值。

## negate

这个方法其实就是test方法的去反，同样的参数入参，调用test方法为true，调用negate方法则为false；

## or

顾名思义，同样的输入参数，两个Predicate的结果一个为true则返回true；

## and

同样的输入参数，两个Predicate的结果两个结果都为true则返回true；

## isEqual

这个方法是Predicate中的一个static方法，是用来比较两个参数的值是否是一样的，猛一看有一种脱裤子放屁的感觉。但是这个方式符合函数式编程的要求，因为这样能够让函数内的值不被在传输过程中发生改变。

```java
Predicate<Integer> hello = Predicate.isEqual("hello");
boolean hello1 = hello.test(1);
System.out.println(hello1);
```

这里需要注意的是isEqual传入的参数是Object类型，并不是T类型的。这也说明了比较的范围是其实任何类型之间的，并不局限于同一类型的。

Objects::isNull这个代码其实就是obj->Objects.isNull(obj)的简写；在java8之后只要是能满足函数式的入参和出参要求的方法，都可以使用函数式编程。

## not

这个方法同样也是一个static方法，作用和isEqual正好相反。

```
Predicate<Object> not = Predicate.not(a -> a.equals("1"));
boolean test1 = not.test("1");
System.out.println(test1);
```

