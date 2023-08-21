## CompletableFuture的使用指南

在我理解看来，CompletableFuture能实现的东西，Future也都能实现，只不过实现的方式没有CompletableFuture优雅。

## 指定线程池来运行任务

```java
        ExecutorService executorService1 = Executors.newSingleThreadExecutor();
        CompletableFuture<String> stringCompletableFuture = CompletableFuture.supplyAsync(() -> {
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        throw new RuntimeException(e);
                    }
                    return "123";
                }, executorService1)
                .thenApplyAsync(result->{
                    System.out.println("执行完成，返回的参数为:"+result);
                    return "success";
                });
// 可以来进行等待任务的执行完成
String join = stringCompletableFuture.join();
```

可以组合两个CompletableFuture任务

```java
// 组合两个 CompletableFuture，并对它们的结果进行合并
        CompletableFuture<Integer> integerCompletableFuture = future1.thenCombine(future2, (result1, result2) -> result1 + result2);
        System.out.println(integerCompletableFuture.get());
```

### 手动指定给任务赋值

```java
public class CompleteExample {
    public static void main(String[] args) {
        // 创建一个未完成的 CompletableFuture
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
            System.out.println("完成hello的逻辑");
            return "hello";
        });

        // 放开这里和不放开这里效果是不一样的
//        future.complete("Hello, CompletableFuture!"); 
        // 启动一个线程，模拟异步任务，并在一段时间后手动完成任务
        new Thread(() -> {
            try {
                future.complete("Hello, CompletableFuture!"); // 手动完成任务并设置结果值
                Thread.sleep(2000); // 模拟异步任务的执行时间

            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();

        // 等待任务完成，并获取结果
        String result = future.join();
        System.out.println("任务结果：" + result);
    }
}
```

上面可以看出来，看谁优先执行，那么结果就是谁；