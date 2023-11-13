# Java8中新加的方法和记录

## 1.flatMap

这个方法适用于流中，并且和map的中间操作其实很像。不同的是flatMap是有一个剥皮的动作，而map则是把流中的元素进行一个简单加工，甚至不动的结果。

map就暂且不说了，我么来说下flatMap这个方法把。

```java
List<List<String>> list=new ArrayList<>();
list.add(Arrays.asList("1","2"));
List<String> list1=list.stream().flatMap(a->a.stream()).collect(Collectors.toList());
System.out.println(list1);
```

以上的方法就把第二层中List<String>数据给脱皮了，并且最后达到了效果；

反过来我们使用map来进行编写一段代码来查看一下：

```java
List<Stream<String>> collect = list.stream().map(a -> a.stream()).collect(Collectors.toList());
```

我们可以看到一个结果，就是map并不会对返回的数据进行"脱皮"。

## 2.computeIfAbsent

这个是Map接口中新加的一个方法；该方法就是如果key存在，那么就把key返回去。如果不存在，则使用当前查找的key进行逻辑运算；

```java
Map<String,String> map=new HashMap<>();
map.put("1","hello");
String s = map.computeIfAbsent("2", a -> a + "-20231031");
System.out.println(s);
```

