## like模糊查询如何优化

1. 使用右边匹配来进行实现，这样可以使用到索引。
2. 如果一定要使用左模糊匹配，那么使用反向索引来进行前匹配。步骤是：创建一个新字段；把字符串进行反转然后进行保存到新字段；在反转的新字段上添加索引。
3. 可以使用es等工具来进行辅助查询。

## count(1),count(*),count(列名)区别

count(1)

作用：是数据库中的所有行都会算作1行。

count(*)

作用：也是统计数据库中的每一行数据，不管是不是null值；效率和count(1)基本保持一致

count(列)

作用：统计该列非空的值；效率比较一般，特别是其中有大量的null值的时候，需要遍历检查每一个值。

## 做过哪些mysql优化

1. 对订单表中的信息，进行了添加索引从而加快查询速度。
2. 通过创建联合索引+查询的覆盖索引来让查询速度变得极其的快，因为这样不需要把所有的数据都给加载出来。
3. 通过对查询的拆分来优化速度，比如有用户表和订单表，原来的逻辑是根据这两个表进行关联，然后再进行堆某个用户的筛选，从而速度比较慢。现在使用先把用户的id给查询出来，然后再对oder表进行查询，从而加快了查询速度。
4. 整个表清洗数据的时候，使用id来作为锚点来进行，然后每次进行大于id，然后再limit，通过这样的方式来把所有的数据给清理结束，然后再保存到另一个表中。
5. 使用truncat来代替delete来清理表数据，这样会让数据真的变少，从而加快了查询速度。

## SQL用了函数一定会索引失效吗

通常情况下，对字段值上添加函数或者进行计算，这个时候会导致字段不走索引，因为索引树都是保存的是最原始的mysql保存的数据，而不是经过处理的数据。

解决这个方式可以先试用代码完成对字段的转化，然后再进行查询。

1. 不要在where的条件当中使用函数操作，可以在查询的条件上使用，这样不会造成索引的失效。
2. 对查询的值做函数操作也可以，但是就是不要对字段进行函数操作。

## 什么时候索引失效反而提升了效率（索引一定会提高查询效率吗）

1. 小表的查询，特别是根据二级索引进行查询，这个时候还涉及到回表操作，有时候反而不如直接去直接扫描小表。
2. 查询返回一个很大量的数据，这个时候也可能进行回表操作，由于数据比较多，从而导致回表
3. 重复数据很多的数据，比如男女字段，是否等字段。
4. 频繁更新的表，因为这样需要去维护mysql的索引，从而降低了效率。

## Mysql深度分页如何优化

1. 基于主键来使用区域查询，这一次的最大值作为下一次查询的开始位置，进行查询。
2. 使用子查询来进行优化，根据子查询把主表的分页数据给查询出来，然后进行进行join.
3. 使用缓存机制，如果业务能够满足要求，可以使用缓存。
4. 如果表的数据几乎不更新，比如历史的财务数据，那么可以直接创建一个表来进行归档，然后根据查询条件来去动态查询某些表。

## MYSQL 8的索引跳跃扫描是什么？

比如mysql8之前的联合索引(a,b),如果where条件中使用b字段来进行查询，那么是使用不到索引的。但是mysql8是可以使用到的，它会根据a创建的索引然后去查询b字段的值，因为b字段的值也是排好序的，所以一旦发现b字段不符合条件的，可以直接结束，然后再查询下一个a字段的值即可。

## 为什么不建议使用存储过程

1. 可移植性差，迁移到不同的数据库可能需要重新写sql
2. 调试过程麻烦，不能设置断点
3. 版本控制不好操作
4. sql太复杂导致可读性差
5. 性能某些情况下可能比较差

## 为什么互联网大厂不建议使用多表join扫描

> 其实这个是根据业务和表设计来做的，才能这么多。单纯的最求不要3个表以上的扫描，而使用单表扫描其实速度并不能快多少。
>
> 特别是有些分页的情况下，业务就是需要从多个表中进行筛选，那么就必须进行多表的扫描
>
> 下面仅仅是面试题而已

1. 性能问题，可能会造成cup和内存的很大开销。
2. 可拓展性差；分布式的mysql中，join变的成本及其高昂。
3. 代码维护性差，特别是复杂的sql
4. 不符合领域模型的设计

## 为什么不推荐使用外键

1. 性能影响；影响插入和删除的时候。特别是大量批量插入的时候，还需要维护外键
2. 灵活性受限
3. 事务的管理变的复杂起来
4. 对分库分表有影响



