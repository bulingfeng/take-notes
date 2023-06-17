# Lambda一些特性

## 局部变量的访问

Lambda表达式可以访问外部的变量，比如：

```java
public static void main(String[] args) {
        int num=123;
        Runnable runnable=()->{
            System.out.println(num);
        };
    }
```

但是Lambda表达式不能修改外部变量的值(包括引用值)，比如

```java
public static void main(String[] args) {
        int num=123;
        Hello hello=new Hello();
        Runnable runnable=()->{
            num=456;// 不能修改
            hello=new Hello();// 不能修改
        };
    }
```

为什么不能赋值呢？

原因是Lambda表达式中的引用的都是临时变量，临时变量值或者引用都存放在占内存中，可能会出现的情况是，lambda访问的变量被回收到导致lambda访问不了该变量，所以lambda访问的都是该局部变量的副本。

如果局部变量仅仅赋值一次那就没有什么区别了——因此就有了这个限制。

不鼓励使用改变外部变量的方式在lambda表达式中进行。因为这样阻碍并行（所以外面的变量都是用Atomicxxx来修饰的，防止多线程修改数据）。

## 方法引用

方法引用其实就是java的一个语法糖，如果不会使用方法引用，可以使用最原始的方式来做，然后转换成方法引用看能否执行。不管是静态方法还是实例方法都可以使用。Lambda等效的例子如下：

![image-20230613225650728](/Users/bulingfeng/Library/Application Support/typora-user-images/image-20230613225650728.png)

比如如下的代码其实就是等效的：

```java
List<Integer> collect1
                = list.stream().map(Integer::parseInt).collect(Collectors.toList());

        List<Integer> collect2
                = list.stream().map(a->Integer.parseInt(a)).collect(Collectors.toList());
```

比如如下的代码也是等效的：

```java
List<String> str=Arrays.asList("a", "b", "A", "B");
str.sort((s1, s2)-> s1.compareToIgnoreCase(s2));
str.sort(String::compareToIgnoreCase);
```

以下代码还是等价的：

```java
public static void main(String[] args) {

        List<String> str=new ArrayList<>();
        str.add("a");
        str.add("b");
        List<String> str2=new ArrayList<>();
        str2.add("a");


        str.removeIf(a->str2.contains(a));
        str.removeIf(str2::contains);
        str.forEach(System.out::println);
    }
```

## 比较链

可以使用lambda表达式来做一些链式的处理。

```java
appleList.sort(Comparator.comparing(Apple::getWeight).reversed().thenComparing(Apple::getName));
```

## 自建流水线

Lambda表达式中的一些函数，其实只要是函数满足其入参和出参的要求都可以使用Lambda表达式来做。基于这个原理我们可以做一些自建的流水线。

```java
public class Letter{
    public static String addHeader(String text){
        System.out.println("addHeader");
        return "From Raoul, Mario and Alan: "+text;
    }


    public static String addFooter(String text){
        return text+" Kind regards";
    }


    public static String checkSpelling(String text){
        System.out.println("checkSpelling");
        return text.replaceAll("labda", "lambda");
    }


    public static void main(String[] args) {
        Function<String, String> addHeader=Letter::addHeader;
        Function<String, String> transformationPipeline
                =addHeader.andThen(Letter::checkSpelling)
                .andThen(Letter::addFooter);

        String apply = transformationPipeline.apply("hello,world");
        System.out.println(apply);
    }
}
```

