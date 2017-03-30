文档见：https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Database%21Connection.php/function/Connection%3A%3Adelete/8.2.x

### 简单的更新操作

用法：
```php
use Drupal\Core\Database\Database;

Database::getConnection($options['target'])->delete($table, $options);
```

如:

```php
$num_updated = Database::getConnection()->delete('node')
  ->condition('nid', 5)
  ->execute();
```

上面的查询将会从节点表中删除nid为5的行。与下面的查询等价:

```php
DELETE FROM {node} WHERE nid=5;
```

* execute()方法将会返回受影响的行数
