# flatMap的使用

简单来说flatMap能把Stream的范型所述的元素给提取出来。比如Hello,World把挨个单词给打印出来；如果使用map则是这样的：

```java
List<String> list=new ArrayList<>();
        list.add("Hello");
        list.add(",");
        list.add("World");
        List<Stream<String>> collect = list.stream().map(a -> a.split(""))
                .map(Arrays::stream)// 把数组给分成挨个的单词
                .collect(toList());
```

如果是使用flatMap呢？

```java
List<String> collect2 = list.stream().map(a -> a.split(""))
                .flatMap(Arrays::stream)// 把数组给分成挨个的单词
                .collect(toList());
```

flatMap过程示意图如下

![image-20230615233420932](/Users/bulingfeng/Library/Application Support/typora-user-images/image-20230615233420932.png)

练习题目1

> 例如，给定列表[1, 2, 3]和列表[3, 4]，应该返回[(1, 3), (1, 4), (2, 3), (2, 4), (3, 3), (3, 4)]

```java
List<Integer[]> collect = numbers1.stream()
                .flatMap(i -> numbers2.stream().map(j -> new Integer[]{i, j}))
                .collect(toList());
        collect.forEach(a-> System.out.println(Arrays.toString(a)));
```

搞不清使用flatMap还是map的时候可以看看返回的参数，如果返回的参数不是你想要的，可以改变下map或者flatMap。

比如使用map的返回参数如下：

```java
List<Stream<Integer[]>> collect = numbers1.stream()
                .map(i -> numbers2.stream().map(j -> new Integer[]{i, j}))
                .collect(toList());
```

练习题目2

> 在原有的基础上，只返回总和能被3整除的数对呢？例如(2, 4)和(3, 3)是可以的。

```java
List<Integer> numbers1=Arrays.asList(1, 2, 3);
        List<Integer> numbers2=Arrays.asList(3, 4);

        List<Integer[]> collect = numbers1.stream()
                .flatMap(i -> numbers2.stream().filter(j -> (i + j) % 3 == 0).map(j -> new Integer[]{i, j}))
                .collect(toList());


        collect.forEach(a-> System.out.println(Arrays.toString(a)));
```

