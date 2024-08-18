## 关于mvcc相关文章

### 1、通俗易懂的mvcc讲解

```
https://www.cnblogs.com/jelly12345/p/14889331.html
```

### 2、mysql如何保证数据不丢失？

先说结论：

> 只有redo log和binlog保证都持久化到磁盘中，这样才能最大程度的保证mysql数据不丢失。配置参数如下：
>
> > 只有在 **sync_binlog 和 innodb_flush_log_at_trx_commit 都等于1的情况下，才能保证数据不丢失。** 即
> >
> > - 写 redo log 时，每次事务提交时，都将所有`redo log` `fsync`到磁盘
> > - 写 binlog 时，每次事务提交时，`binlog` 都会执行 `fsync`到磁盘。

redo log的名词解释

> 系统崩溃重启需要按照上述内容所记录的步骤重新更新数据页，所以上述内容也被称为重做日志，即redo log.

[mysql各种日志的解释和作用](https://xie.infoq.cn/article/e59636fd19dc1963f6d017d55#:~:text=1%EF%B8%8F%E2%83%A3%20redo%20%E6%97%A5%E5%BF%97%E7%9A%84%E4%BD%9C%E7%94%A8&text=redo%20%E6%97%A5%E5%BF%97%E8%AE%B0%E5%BD%95%E4%BA%8B%E5%8A%A1%E6%89%A7%E8%A1%8C,%E6%8C%81%E4%B9%85%E6%80%A7%E8%BF%99%E4%B8%80%E7%89%B9%E6%80%A7%E3%80%82)

