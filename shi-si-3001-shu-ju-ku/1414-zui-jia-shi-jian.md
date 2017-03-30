在本章我们看到许多将数据库API函数连写的代码，如:

```php
<?php
$result = db_select('mytable')
  ->fields('mytable')
  ->condition('myfield', 'myvalue')
  ->execute();
?>
```

注意: 并不是所有的数据库函数或方法都支持链式写法。

如:

```php
<?php
$query = db_select('mytable');
$query->addField('mytable', 'myfield', 'myalias');
$query->addField('mytable', 'anotherfield', 'anotheralias');
$result = $query->condition('myfield', 'myvalue')
  ->execute();
?>
```

注意: 如果一个函数不可连写，意思是在它后面不能连写其它函数，但在它前面仍然可以连写其它函数。

### 可以连写的函数
```bash
addMetaData()
addTag()
comment()
condition()
countQuery()
distinct()
exists()
fields()
forUpdate()
groupBy()
having()
havingCondition()
isNotNull()
isNull()
notExists()
orderBy()
orderRandom()
range()
union()
where()
execute()
```

### 不可连写的函数:
```bash
addExpression()
addField()
addJoin()
extend()
innerJoin()
join()
leftJoin()
rightJoin()
```
