# Lambda函数式接口

Lambda函数式接口的意义是什么呢？是为了炫技？为了迎接函数式编程？还是能够带来试用的变化来简化我们的代码。

比如我们要读取某个文件第一行内容。那么那么我们可以进行如下编程：

```java
    public static void readFile() throws IOException {
        String path="/Users/bulingfeng/Desktop/test/test.txt";
        try (BufferedReader reader = new BufferedReader(new FileReader(path))) {
            System.out.println(reader.readLine());
        }
    }
```

那么假如有个新需求，这个新需求要求读取两行的文件文件内容的时候怎么办呢？这个时候我们需要再写一个方法，来读取两行的内容是吗？当然也可以但是这不是产生了很多不必要的工作量。

```java
    public static void readFileTwoLine() throws IOException {
        String path="/Users/bulingfeng/Desktop/test/test.txt";
        try (BufferedReader reader = new BufferedReader(new FileReader(path))) {
            System.out.println(reader.readLine());
            System.out.println(reader.readLine());
        }
    }
```

其实这个时候我们发现，读取的内容的逻辑可能会千变万化，比如在读取内容进行一些额外处理呢？还要再写一个方法吗？这个时候我们就需要把读取的逻辑抽离出来，来应对万变的需求。

```java
    public static  void actionConsumer(Consumer<BufferedReader> consumer) throws FileNotFoundException {
        String path="/Users/bulingfeng/Desktop/test/test.txt";
        BufferedReader reader=new BufferedReader(new FileReader(path));
        consumer.accept(reader);
    }
```

测试方法为：

```java
    public static void main(String[] args) throws IOException {
        Consumer<BufferedReader> consumer=bufferedReader -> {
            try {
                System.out.println(bufferedReader.readLine());
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        };

        actionConsumer(consumer);
    }
```

