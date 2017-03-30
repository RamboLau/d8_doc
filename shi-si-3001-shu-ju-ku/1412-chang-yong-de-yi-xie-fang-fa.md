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

#### 嵌套
```php
$or = db_or()->condition('field2', 5)->condition('field3', 6);
$query
  ->condition('field1', [1, 2], 'IN')
  ->condition($or);
// Results in:
// (field1 IN (:db_placeholder_1, :db_placeholder_2) AND (field2 = :db_placeholder3 OR field3 = :db_placeholder_4))
```

#### 检测NULL值
```php
$query->isNull('myfield');
// Results in (myfield IS NULL)

$query->isNotNull('myfield');
// Results in (myfield IS NOT NULL)
```

###2、统计行数
```php
$num_rows = $query->countQuery()->execute()->fetchField();
```

###3、排除重复值
* DISTINCT可能引起性能问题，尽量不要使用
```php
$query->distinct();
```

###4、表达式(Expressions)
```php
$count_alias = $query->addExpression('COUNT(uid)', 'uid_count');
$count_alias = $query->addExpression('created - :offset', 'timestamp', [':offset' => 3600]);
```

###5、分组查询
```php
$query->groupBy('uid');
```
如：
```php
$query = $database->select('node', 'n')
   ->fields('n', ['uid']);
$query->addExpression('count(uid)', 'uid_node_count');
$query->groupBy('n.uid');
$query->having('COUNT(uid) >= :matches', array(':matches' => 2));
$results = $query->execute();
```
相当于:
```php
SELECT n.uid, count(uid) as uid_node_count FROM node n GROUP BY n.uid HAVING COUNT(uid) >= 2
```

###6、join
```php
$query = $database->select('node', 'n');
$table_alias = $query->join('users', 'u', 'n.uid = u.uid AND u.uid = :uid', array(':uid' => 5));
```
相当于：
```php
SELECT * FROM node n JOIN users u ON n.uid = u.uid AND u.uid = 5;
```