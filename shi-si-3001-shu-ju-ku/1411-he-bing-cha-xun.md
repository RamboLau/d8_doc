合并查询是一种特殊类型的混合查询。虽然在SQL2003中定义了它的语法，但是很少有数据库支持它。大多数的数据库使用了不同的语法实现了这一功能。Drupal的数据库抽象层提供了合并查询建立器，由它提供了统一的合并查询方法并将其编译成适合于数据库的特定语法。

### merge
一般来说，合并查询指的是INSERT查询和UPDATE查询的联合。如果出现了指定的条件，如使用主键匹配到需更新的行，则运行一个UPDATE查询。如果没有出现指定的条件，则运行插入查询。即存在则更新，不存在则插入。它与下面的代码等价:

```php
if ($database->select("SELECT COUNT(*) FROM {example} WHERE id = :id", array(':id' => $id))->fetchField()) {
  // Run an update using WHERE id = $id
}
else {
  // Run an insert, inserting $id for id
}
```

它的真正实现随着数据库的不同而不同。注意当合并查询是一个原子操作时，它的真正实现依赖于具体的数据库。MySQL将它实现为原子操作，因为它提供了UPDATE...INSERT语句，而上面的代码例子则不是。

下面列出合并查询的用法:

```php
Database::getConnection()->merge('example')
  ->key(['name' => $name])
  ->fields([
    'field1' => $value1,
    'field2' => $value2,
  ])
  ->execute();
```  

如果存在与$name匹配的行，则使用字段列表中的字段及其值进行更新。如果这行不存在，则插入一个新行，字段包括name，其值为$name。因此查询最终的执行方式取决于该行是否存在。

有时，你在更新某些字段的值时可能需要测试记录是否存在，这需使用key()方法。有两种使用方式。

```php
Database::getConnection()->merge('example')
  ->insertFields([
    'field1' => $value1,
    'field2' => $value2,
  ])
  ->updateFields([
    'field1' => $alternate1,
  ])
  ->key(['name' => $name])
  ->execute();
```
上面的例子表明，如果需插入的记录已经存在则更新它的field1字段。否则插入该条记录。

```php
Database::getConnection()->merge('example')
  ->key([
    'name' => $name
  ])
  ->fields([
    'field1' => $value1,
    'field2' => $value2,
  ])
  ->expression('field1', 'field1 + :inc', [':inc' => 1])
  ->execute();
```
在这个例子中，如果$name记录已经存在，field1将会加1。这常用于记数，当某些特殊事件发生时更新记数。不管记录是否存在field2都被设置为同一个值。

注意: 
* 可以调用expression()多次，为每个需设置成表达式的字段调用一次。它的第一个参数是字段，第二个参数指出表达式，第三个参数是可选的，它提定一个占位符数组，其值用于替换表达式中的占位符。
* 如果一个字段设置成expression()，则它优先于updateFields()
* 如果在updateFields()中指定更新的字段和值，这些字段只有记录存在时才会更新。没有在updateFields()中指定的字段则不受影响
 
### upsert
文档见：https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Database%21Query%21Upsert.php/class/Upsert/8.2.x

指一个抽象的"UPSERT"（更新或插入）查询操作。
要使用这个方法，表必须要有主键。

* 根据条件判断有无记录，有的话就更新记录，没有的话就插入一条记录。
* key()用来定义一个已经存在的row, 必须是unique类型，不然永远执行Insert操作.

如:
```php
$query = $database->upsert('flood');
$query->fields([
  'fid',
  'identifier',
]);
$query->values([
  1,
  'My indentifier for upsert'
]);
$query->key('fid');
$query->execute();
```
