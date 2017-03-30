从结果集中获取数据有多种方式，视具体的情况而定。

如:

```php
$record = $result->fetch();        // 缺省模式
$record = $result->fetchObject();  // 返回一个记录对象
$record = $result->fetchAssoc();   // 返回一个关联数组
```

如果结果集中没有记录，则会返回FALSE。应尽量避免使用fetch()而使用fetchObject()和fetchAsscoc，因为后者带有自身描述更易于理解。如果你使用其它的PDO支持的fetch模式，请使用fecth()。

### 从结果集中获取某列
```php
$record = $result->fetchField($column_index);
```
列索引$column_index的默认值为0，表示第一列。

### 统计受影响的行数
```php
$number_of_rows = $result->rowCount();
```

### 统计选择查询返回的行数
```php
$number_of_rows = db_select('node')->countQuery()->execute()->fetchField();
```

### 所有记录
```php
// Retrieve all records into an indexed array of stdClass objects.
$result->fetchAll();
```
如:
```php
// Get an array of arrays with both numeric and associative keys.
$result->fetchAll(PDO::FETCH_BOTH);
```

### 结果集为特定的字段
```php
// Retrieve all records into an associative array keyed by the field in the result specified.
$result->fetchAllAssoc($field);
```
如:
```php
// Get an array of arrays keyed on the field 'id'.
$result->fetchAllAssoc('id', PDO::FETCH_ASSOC);
```

### 结果集为2列关联数据
```php
// Retrieve a 2-column result set as an associative array of field 0 => field 1.
$result->fetchAllKeyed();

// You can also specify which two fields to use by specifying the column numbers for each field
$result->fetchAllKeyed(0, 2); // would be field 0 => field 2
$result->fetchAllKeyed(1, 0); // would be field 1 => field 0

// If you need an array where keys and values contain the same field (e.g. for creating a 'checkboxes' form element), the following is a perfectly valid method:
$result->fetchAllKeyed(0, 0); // would be field 0 => field 0, e.g. [article] => [article]
```

### 单行记录
```php
// Retrieve a 1-column result set as one single array.
$result->fetchCol();

// Column number can be specified otherwise defaults to first column
$result->fetchCol($column_index);
```

### 单个对象记录
```php
$result->fetchObject();
```

 

fetchAll()和fetchAllAssoc使用的是默认的fetch模式(索引数组、关联数组或者对象)。这是可能通过设置fetch模式常量进行修改。对于fetchAll()方法fetch模式对应第一个参数，对于fetchAllAssoc()方法它对应第二个参数。例子:


因为PHP在返回对象上支持链式方法调用，所以经常会跳过$result变量。请看:
// Get an associative array of nids to titles.
$nodes = db_query("SELECT nid, title FROM {node}")->fetchAllKeyed();

// Get a single record out of the database.
$node = db_query("SELECT * FROM {node} WHERE nid = :nid", array(':nid' => $nid))->fetchObject();

// Get a single value out of the database.
$title = db_query("SELECT title FROM {node} WHERE nid = :nid", array(':nid' => $nid))->fetchField();
如果你想使用一个像array(1,2,3,4,5)这样的简单数组，并且想设置成这样array(1=>1,2=>2,3=>3,4=>4,5=>5)。你可以这样使用:
$nids = db_query("SELECT nid FROM {node}")->fetchAllKeyed(0,0);