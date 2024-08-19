## 关于mvcc相关文章

### 1、通俗易懂的mvcc讲解

```
https://www.cnblogs.com/jelly12345/p/14889331.html
```

### 2、mysql如何保证数据不丢失？

其实在讨论不丢失的时候，首先要定义什么是数据不丢失。我的判断是这样的：

> 如果mysql数据库提交了事务，并且没有报任何异常的情况下，这个时候如果数据库发生了各种意外而造成的数据库丢失才是真的数据库丢失。
>
> 如果是一个应用调用了数据库接口，然后在调用的过程中数据库发生了异常，这个时候数据没有写入到数据库中，这个就不能成为数据丢失。
>
> 换句话说，只要应用程序成功提交了事务，那么这个时候的数据库发生丢失才能叫数据库数据丢失。不过这里也有特殊情况，就是在事务提交的过程中，数据库发生了意外，这个时候如果redo log和bin log都把对应的数据刷到了磁盘，那么数据也照样是可恢复的。

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

### 3、redo log 和binlog的解释

redo log和bin log都是使用的WAL（write ahead logging）技术，也就是说会先写日志再刷盘，这里的先写日志是指把日志写到内存中去，而内存有分为两部分(buffer和page cach)。

redo log的产生目的就是为了防止数据库突然发生异常而造成的数据丢失。

**关于redo log的日志写入**

下图是redo log的文件的数据结构，redo log的文件是一个环型结构（当然逻辑上是一个环型结构）;

write pos是记录当前的位置，checkpoint是要擦拭文件的位置，write pos和checkpoint之间的就是可以写入文件的内容。

redo log的写入流程是这样的：

> 当执行update语句的时候，引擎会把新数据写入到内存中，同时更新到redo log的内存日志当中，此时的redo log是出于prepare阶段，这里的prepare阶段意思是:"已经准备好了，随时可以进行日志提交"；
>
> 然后这个时候binlog的日志会写到内存中去，然后把进行提交，随后把redolog的日志状态给改成commit.

![](https://bulingfeng.com/技术相关/mysql/image/1-redo-log-write.png)

**bin log**

> redo log是引擎端的日志，而bin log是server端的日志；

**redo log和bin log的不同**

> - redo log 是 InnoDB 引擎特有的；binlog 是 MySQL 的 Server 层实现的，所有引擎都可以使用。
>
> - redo log 是物理日志，记录的是“在某个数据页上做了什么修改”；binlog 是逻辑日志，记录的是这个语句的原始逻辑，比如“给 ID=2 这一行的 c 字段加 1 ”。
> - redo log 是循环写的，空间固定会用完；binlog 是可以追加写入的。“追加写”是指 binlog 文件写到一定大小后会切换到下一个，并不会覆盖以前的日志。

参考文章：

- [redolog和binlog的写入流程](https://juejin.cn/post/7019969643657822216)
