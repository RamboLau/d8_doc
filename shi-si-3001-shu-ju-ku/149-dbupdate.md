文档见：https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Database%21Connection.php/function/Connection%3A%3Aupdate/8.2.x

### 简单的更新操作

用法：
```php
use Drupal\Core\Database\Database;

Database::getConnection($options['target'])->update($table, $options);
```

如:

```php
$num_updated = Database::getConnection()->update('node')
  ->fields([
    'uid' => 5,
    'status' => 1,
  ])
  ->condition('created', REQUEST_TIME - 3600, '>=')
  ->execute();
```

* execute()方法将会返回查询所影响的行数

上面的例子与下面的查询等价:

```php
UPDATE {node} SET uid=5, status=1 WHERE created >= 1490861355;
```

execute()方法将会返回查询所影响的行数。注意受影响行数与匹配的行数是不同的。在上例，uid为5 status为1的记录也会匹配到，但查询不会修改它们，因此它们是不受影响的。

<?php
$query = db_update('mytable');
// Conditions etc.
$affected_rows = $query->execute();
?>

应用where条件:

$query = db_update('block')
  ->condition('module', 'my_module')
  ->where(
    'SUBSTR(delta, 1, 14) <> :module_key',
    array('module_key' => 'my_module-key_')
  )
  ->expression('delta', "REPLACE(delta, 'my_module-other_', 'my_module-thing_')")
  ->execute();

在条件中使用字符串函数

$query = db_update('block')
  ->condition('module', 'my_module')
  ->condition('SUBSTR(delta, 1, 14)', 'my_module-key_', '<>') // causes error.
  ->expression('delta', "REPLACE(delta, 'my_module-other_', 'my_module-thing_')")
  ->execute();

上面的代码使用了condition()方法添加条件，根据condition()方法的参数解释，它的第一个参数是字段名，在字段名中要使用操作符或函数请使用where()。所以上面的代码causes error。
