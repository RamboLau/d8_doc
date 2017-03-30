文档见：https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Database%21Connection.php/function/Connection%3A%3Adelete/8.2.x

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



DELETE查询也需要使用一个查询建对象。使用db_delete()函数进行DELETE操作。

$query = db_delete('node', $options);

上面的代码创建一个DELETE查询对象，其功能是从节点表中删除记录。同样地，表名不需要使用大括号，查询建立器会自动处理。

DELETE查询对象也使用流式API，除execute()方法外，其它方法返回查询对象自身，因此可以在它上面链式调用方法。这样不需要保留查询对象作为中间变量。

DELETE查询是非常简单的，它仅仅由WHERE子句组成。WHERE子句的结构同Conditional子句。

下面是一个完整的DELETE查询:

$num_deleted = db_delete('node')
  ->condition('nid', 5)
  ->execute();

上面的查询将会从节点表中删除nid为5的行。与下面的查询等价:

DELETE FROM {node} WHERE nid=5;

execute()方法将会返回受影响的行数。
