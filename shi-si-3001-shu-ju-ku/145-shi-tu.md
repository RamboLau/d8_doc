视图是表示预定义查询结果的虚拟表，用户可以从这个虚拟表中查询，就像从真正的数据库表中查询一样。

创建一个SQL视图

```php
CREATE VIEW {view_name} AS SELECT something FROM {table_name};
```

从SQL视图中查询数据

```php
SELECT something FROM {view_name};
```

为什么要使用视图?
* 视图能将多个表连接成一个表
* 视图只是相关表的一个子集
* 视图可以表示复杂的聚合数据(sum,average,...)
