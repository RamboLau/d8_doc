文档见: https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Database%21Query%21Insert.php/class/Insert/8.2.x
### 简单的插入操作

用法：
```php
use Drupal\Core\Database\Database;

Database::getConnection($options['target'])->insert($table, $options);
```

如:

```php
$query = Database::getConnection()->insert('node', $options);
```

上面的代码创建一个INSERT查询对象，它将向节点表插入一条或多条记录。

注意: 表名不需要使用大括号，因为查询构建器会自动地处理它。


### 推荐写法

 
紧凑形式:

```php
$nid = Database::getConnection()->insert('node')
  ->fields(array(
    'title' => 'Example',
    'uid' => 1,
    'created' => REQUEST_TIME,
  ))
  ->execute();
```

* fields: 关联数组，键对应数据库的字段名，值对应字段值
* execute: 调用了这个方法，查询才会正常执行
* 如果表含有自增字段，则会返回自动增加的字段值，否则无任何返回值

上面的查询代码与以下SQL语句相同:

```php
INSERT INTO {node} (title, uid, created) VALUES ('Example', 1, 1221717405);
```

### 冗余形式
这种方式不推荐！
```php
$nid = Database::getConnection()->insert('node')
  ->fields(array('title', 'uid', 'created'))
  ->values(array(
    'title' => 'Example',
    'uid' => 1,
    'created' => REQUEST_TIME,
  ))
  ->execute();
```  

这种形式与前面的形式相比，虽效果一样，但显得更冗长。

 
### 多行插入形式

INSERT查询对象也可以一次性插入多条记录。只须多次调用values()方法将它们加入到查询表达式队列并将它们组合在一起。对于大多数数据库，将会一次性插入多条记录以提高数据插入速度。在MySQL中，它将会使用MySQL多值插入语法。

```php
$values = array(
  array(
    'title' => 'Example',
    'uid' => 1,
    'created' => REQUEST_TIME,
  ),
  array(
    'title' => 'Example 2',
    'uid' => 1,
    'created' => REQUEST_TIME,
  ),
  array(
    'title' => 'Example 3',
    'uid' => 2,
    'created' => REQUEST_TIME,
  ),
);
$query = Database::getConnection()->insert('node')->fields([
  'title', 
  'uid', 
  'created'
]);
foreach ($values as $record) {
  $query->values($record);
}
$query->execute();
```

上面的代码将会把插入的三条记录组合在一起执行，为了更有效地执行，这需要使用相应的数据库驱动的插入方法。注意上面的代码我们将查询对象保存在变量中以使我们通过循环调用values()构造多行查询。

上面的代码与以下三个查询相等:

INSERT INTO {node} (title, uid, created) VALUES ('Example', 1, 1221717405);
INSERT INTO {node} (title, uid, created) VALUES ('Example2', 1, 1221717405);
INSERT INTO {node} (title, uid, created) VALUES ('Example3', 2, 1221717405);

执行多行查询的execute()方法的返回值没有定义并且是不可信的，因为它随着数据库的不同而不同。

 
基于SELECT查询的INSERT

如果你想处理的一个表带有其它表数据，你可能需要在源表上执行SELECT查询，然后在PHP中遍历数据并将其插入新表，或者使用INSERT INTO...SELECT语句将SELECT返回的每行作为INSERT查询的数据源。

举一个例子，我们想建立一个”mytable”表，它包含节点id(nid)和用户名(name)，这个表的数据来源于节点表和用户表，并且针对于特定的页面类型。

<?php
// Build the SELECT query.
$query = db_select('node', 'n');
// Join to the users table.
$query->join('users', 'u', 'n.uid = u.uid');
// Add the fields we want.
$query->addField('n','nid');
$query->addField('u','name');
// Add a condition to only get page nodes.
$query->condition('type', 'page');

// Perform the insert.
db_insert('mytable')
  ->from($query)
  ->execute();
?>

 
缺省值

在正常情况下，如果你没有为给定的字段指定一个值，由schema定义的数据库表将智能地为其应用一个缺省值。然而有时，你需要明确地通知数据库应用一个默认值。比如你想为整个记录应用默认值。要明确地指定默认值，可以使用useDefaults()方法。

$query->useDefaults(array('field1', 'field2'));

这一行通知查询使用数据库为field1和field2定义的默认值。注意在useDefaults()和fields()或values()指定相同字段会导致一个错误，并抛出一个异常。
