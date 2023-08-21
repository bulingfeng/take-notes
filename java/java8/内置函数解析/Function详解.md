从Java8开始函数式编程在java中就开始变的流行了起来，但是大多数情况下，我们只是简单的使用一些排序，收集等函数。而没有对真正的函数式编程进行内部的消化，以至于看外国项目的代码的时候（采用比较复杂的函数式编程的时候），就会感觉自己的知识突然不够用了。

于是自己打算做一个全面的函数式编程的解析，从而能让自己真正的理解函数式编程的内部设计，以及复杂的使用方法。其中一些代码不能保证百分之百正确但是能保证百分之百用心。

今天就来探讨下Function这个类。

Function的源代码如下：

```java
@FunctionalInterface
public interface Function<T, R> {

    R apply(T t);

    default <V> Function<V, R> compose(Function<? super V, ? extends T> before) {
        Objects.requireNonNull(before);
        return (V v) -> apply(before.apply(v));
    }

    default <V> Function<T, V> andThen(Function<? super R, ? extends V> after) {
        Objects.requireNonNull(after);
        return (T t) -> after.apply(apply(t));
    }

    static <T> Function<T, T> identity() {
        return t -> t;
    }
}
```

首先`R apply(T t);`这个方法比较简单，在这里就不做介绍了；`identity`方法也比较简单，也不做介绍了；现在着重来分享两个方法；

## compose

首先这个方法引入了一个`V`的范型。这范型提高了整个方法的灵活度，意味着before可以输入任何类型的对象，只要保证before返回的参数为`T`类型的子类即可（并且这个必须是严格保证的）。

`Function<? super V, ? extends T> before`这句话中的的解释是这样的：

由于返回参数`Function<V, R>`已经固定。

> ? super V ，输入的值为V的父类，这样也无需进行强转，并且还扩大了输入范围。
>
> ? extends T 返回的参数是在T的范围之内，这样返回值也能被轻松返回，也无需做额外处理。假设返回的是T的父类怎么办呢？是不是直接报错了。

从另一个更简单的角度来进行分析:

> apply(before.apply(v)); 这句话中有一个apply方法，这么意味着因为apply正常来说接受的参数应该为T，这里为了扩大输入范围，可以输入T的父类。也就意味着before函数的返回值必须为T的子类。从这个角度来分析，以下代码就好理解了。
>
> Function<? super V, ? extends T> before

额外的小事情，范型的函数好像应该是至少返回一个范型，如果某个范型不是类中定义的，那么就必须声明处理比如`<V>`。如果返回和入参都是类中表明的，那么直接返回对应一个范型即可，比如`<T> Function<T, T> identity(`

## andThen

分析完compose，那么andThen其实也是一个套路，只不过是执行的顺序不一样了而已。

下面来举一个例子

```java
public class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }


    public static void main(String[] args) {
        Function<Person, String> getName = Person::getName;
        Function<String, String> toUpperCase = String::toUpperCase;
        Function<String, Integer> getLength = String::length;

        Function<Person, Integer> composedFunction = getName.andThen(toUpperCase).andThen(getLength);

        Person person = new Person("Alice", 30);
        int result = composedFunction.apply(person);

        System.out.println("Name: " + person.getName() + " -> Uppercase Length: " + result);
    }
}
```

首先`Function<Person, String> getName = Person::getName;`函数确定了输入参数为`Person`,`Function<String, Integer> getLength = String::length;`函数决定了返回的参数为Integer，所以最后的调用函数链为:

```java
Function<Person, Integer> composedFunction = getName.andThen(toUpperCase).andThen(getLength);
```

并且最终的函数内容为:`Function<Person, Integer>`,也就是输入一个`Pershon`类，最后得到一个`Integer`类型。