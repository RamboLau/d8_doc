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

有的函数不能连着写，如下面代码:

<?php
$query = db_select('mytable');
$query->addField('mytable', 'myfield', 'myalias');
$query->addField('mytable', 'anotherfield', 'anotheralias');
$result = $query->condition('myfield', 'myvalue')
  ->execute();
?>

函数是否能连写，关键要看它的返回值和需连写的函数，它的返回值是查询对象并且需调用的函数是它的方法，则该函数可以连写。你可以在execute()后附加一个结果集函数如fetchField()，请看下面的代码:

<?php
$number_of_records = db_select('mytable')
  ->condition('myfield', 'myvalue')
  ->countQuery()
  ->execute()
  ->fetchField();
?>

如果要找出一个数据库API函数的返回值，请看api.drupal.org的数据库API。

下面列出了一些可以连写的函数列表，当然这里并没有覆盖到所有的函数，但对于大多数情况已经够用了。

注意:如果一个函数不可连着写，意思是在它后面不能连写其它函数，但在它前面仍然可以连写其它函数。

可以连写的函数

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

不可连写的函数:

    addExpression()
    addField()
    addJoin()
    extend()
    innerJoin()
    join()
    leftJoin()
    rightJoin()
