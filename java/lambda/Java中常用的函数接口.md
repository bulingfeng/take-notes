# Java中常用的函数接口

## Predicate

其部分源码如下：

```java
@FunctionalInterface
public interface Predicate<T> {

    /**
     * Evaluates this predicate on the given argument.
     *
     * @param t the input argument
     * @return {@code true} if the input argument matches the predicate,
     * otherwise {@code false}
     */
    boolean test(T t);
}
```

那么我们可以使用Predicate来实现一个filter。

```java
public class PredicateTest {
    public static void main(String[] args) {
        Predicate<String> predicate=s -> !s.equals("123");
        List<String> list= Arrays.asList("123","456","789");
        List<String> result = filter(list, predicate);
        // forEach里面是接收一个consumer，但是只要满足consumer格式的
        // 也就是说有入参没有出参数的都可以当做consumer使用
        result.stream().forEach(PredicateTest::print);
//        result.stream().forEach(System.out::println);
    }




    public static <T> List<T> filter(List<T> list, Predicate<T> predicate){
        List<T> result=new ArrayList<>();

        for (T item : list) {
            if (predicate.test(item)){
                result.add(item);
            }
        }

        return result;
    }


    public static void print(String params){
        System.out.println(params);
    }
}
```

