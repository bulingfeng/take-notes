## Lambda表达式

lambda表达式可以让很多事情变的简单起来和容易理解，在没有lambda之前，java则是使用匿名函数(anonymous function);

下面两个案例来说明lambda表达式是如何让事情变的简单起来的；

```java
public enum MathEnum1 {
    PLUS("+"){
        @Override
        public double apply(double x, double y) {
            return x+y;
        }
    },
    DIVIDE("/"){
        @Override
        public double apply(double x, double y) {
            return x/y;
        }
    }
    ;
    private String code;
    MathEnum1(String code) {
        this.code = code;
    }
    public abstract double apply(double x, double y);

    public static void main(String[] args) {
        double plusValue = PLUS.apply(1, 3);
        double divideValue = DIVIDE.apply(6, 2);
        System.out.println(plusValue);
        System.out.println(divideValue);
    }
}

```

> 说实话，以上的写法我不是很理解和看的懂，全当一种固定语法得了，这种方式让人看的云里雾里。但是你又知道肯定是使用了匿名内部来实现了apply方法，但是为什么这个枚举这么写其实你还是不太懂的，也不是很容易理解的；

使用lambda表达式来描述

```java
public enum MathEnum2 {
    PLUS("+",(x,y)->x+y);
    private String code;

    private DoubleBinaryOperator op;

    MathEnum2(String code, DoubleBinaryOperator op) {
        this.code = code;
        this.op = op;
    }

    public String getCode() {
        return code;
    }


    public DoubleBinaryOperator getOp() {
        return op;
    }


    public  double apply(double x,double y){
        return op.applyAsDouble(x,y);
    }

    public static void main(String[] args) {
        double num1 = MathEnum2.PLUS.apply(1, 2);
        double num2 = MathEnum2.PLUS.getOp().applyAsDouble(1,2);
        System.out.println(num1);
        System.out.println(num2);
    }
}
```

这个方式就变得极其容易理解和明了。PLUS里面就是两个参数，只不过第二个变成了对应的**DoubleBinaryOperator** 而已（其实可以是任意二元的函数即可，只要你愿意）；

那么这一切就变得极其容易了，详细的案例是在main方法中

> double num1 = MathEnum2.PLUS.apply(1, 2); 
>
> 这个方式是通过使用枚举内部的方法来进行执行的。
>
> 而二种方式也更好理解，相当于拿到了对应的DoubleBinaryOperator，然后再赋值计算而已。

## lambda表达式的简写方式

|  Method Ref Type  |        Example         |                 Lambda equivalent                  |
| :---------------: | :--------------------: | :------------------------------------------------: |
|      static       |   Integer::parentInt   |              str->Inrteger::parseInt               |
|       bound       | Instant.now()::isAfter | Instant then = Instant.now(); t -> then.isAfter(t) |
|      unbound      |  String::tolowerCase   |                str->str.toLowerCase                |
| class constructor |   TreeMap<K,V>::new    |               () -> new TreeMap<K,V>               |
| array constructor |       int[]::new       |                len -> new int[len]                 |

其实method references 可以使用更好的方式来代替lambda表达式，并且还是很灵活的，只要接受的参数符合Function这类函数的入参和返回参数即可。

## Lambda表达式和匿名函数之间的区别

1. 使用javac来进行编译lambda表达式的时候，会产生两个.class文件。其中一个是文件名$1.class的字节码文件。而这个文件只有是lambda表达式的时候才会有。
2. 使用lambda表达式的要求是interface并且其中还必须有且仅有1个抽象函数。而匿名函数可以有多个。
3. lambda表示式让书写和看起来更简单，而匿名函数(anonymous method)让人感觉很冗余。

疑问：

> 为何使用javac来编译带有lambda表达式的类会出现带有*$1.class文件，而使用idea的maven来编译则没有这个文件呢？

## 学习和使用lambda表达式的关键

其实在`java.until.function`这个包里面其实有43个函数，而这43个函数不期望你记住所有。但是你需要记住里面的6个重要函数。那么剩下的所有函数你都可以进行融会贯通。

| Interface         | Function Signature | Example             |
| ----------------- | ------------------ | ------------------- |
| UnaryOperator<T>  | T apply(T t)       | String::toLowerCase |
| BinaryOperator<T> | T apply(T t1,T t2) | BigInteger::add     |
| Predicate<T>      | boolean test(T t)  | Collection::isEmpt  |
| Function<T,R>     | R apply(T t)       | Arrays::asList      |
| Supplier<T>       | T get()            | Instant::now        |
| Consumer<T>       | void accpet(T t)   | System.out::println |

疑问：

> 如何使用LinkHashMap来实现一个当map得到100后，然后把最后的数据都给删除掉。
