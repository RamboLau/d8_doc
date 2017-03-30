#1、条件子句

```php
$query->condition($field, $value = null, $operator = '=')

$query->where($snippet, $args = array());

$query->havingCondition($field, $value = NULL, $operator = '=');
$query->having($snippet, $args = array());
```

如：
```php
$query->condition('myfield', [1, 2, 3], 'IN');
// Becomes: myfield IN (:db_placeholder_1, :db_placeholder_2, :db_placeholder_3)

$query->condition('myfield', [5,10], 'BETWEEN');
// Becomes: myfield BETWEEN :db_placeholder_1 AND :db_placeholder_2

$query->condition('myfield', [1, 2, 3], 'NOT IN');
```

也支持嵌套：
```php
$or = db_or()->condition('field2', 5)->condition('field3', 6);
$query
  ->condition('field1', [1, 2], 'IN')
  ->condition($or);
// Results in:
// (field1 IN (:db_placeholder_1, :db_placeholder_2) AND (field2 = :db_placeholder3 OR field3 = :db_placeholder_4))
```

