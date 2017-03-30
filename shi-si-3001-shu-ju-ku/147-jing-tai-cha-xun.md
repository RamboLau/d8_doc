### query方法
只进行简单的SELECT查询，可以使用静态查询，不要使用它来执行INSERT、UPDATE、DELETE等查询，这些查询应该通过db_insert、db_update、db_delete来完成。

如：
```php
use Drupal\Core\Database\Database;

Database::getConnection($target)->query($query, $args, $options);
```

query方法包含三个参数:
* $query: 需运行的查询字符串，在字符串中使用占位符代替变量使用，表名使用大括号{}，主要为了让数据库系统能向表名添加合适的前缀
* $args: 用来替换占位符的数组
* $options: 一个控制查询如何执行的可选数组

 
### 占位符

占位符是在查询字符串中使用一个由冒号和一个有意义的字符串组成，在查询执行时它会被替换成真正的值。这样做是主要是为了防止SQL注入攻击。

```php
$result = Database::getConnection()->query("SELECT nid, title FROM {node} WHERE created > :created", [
  ':created' => REQUEST_TIME - 3600,
]);
```

* :created 将会在执行查询时被动态地替换成REQUEST_TIME – 3600。

一个查询中可以有多个占位符，但是所有的占位符必须有唯一的名称。根据具体的使用情况，占位符可以嵌入代码中(如上例)，也可以定义一个占位符数组变量并传入，数组中占位符的顺序不用管。

以”db_”开始的占位符是为内部系统预留的，请不要显示地指定。

注意占位符不能转义也不能使用引号。因为它们被单独地传递给数据库服务器，服务器能区分查询字符串和占位符的值。

// 错误 (:type占位符使用了引号)
$result = db_query("SELECT title FROM {node} WHERE type = ':type'", array(
  ':type' => 'page',
));

// 正确 (:type占位符没有使用引号)
$result = db_query("SELECT title FROM {node} WHERE type = :type", array(
  ':type' => 'page',
));

占位符不能用于列名和表名。如果表名来自于不安全的输入，应该使用db_escape_table()，这个函数会返回一个安全的表名。

 
占位符数组

Drupal的数据库层包含了占位符的一个额外功能。如果传递给占位符的是一个数组，它将会被自动地展成以逗号分开的列表以响应占位符。这意味着开发者不必担心它们需要多少个占位符。

请看下面的例子以便于更好地理解:

Drupal 7:
db_query("SELECT * FROM {node} WHERE nid IN (:nids)", array(':nids' => array(13, 42, 144)));

Drupal 8:
db_query("SELECT * FROM {node} WHERE nid IN (:nids[])", array(':nids[]' => array(13, 42, 144)));

上面的查询表达式与下面的查询表达式是一致的:

db_query("SELECT * FROM {node} WHERE nid IN (:nids_1, :nids_2, :nids_3)", array(
  ':nids_1' => 13,
  ':nids_2' => 42,
  ':nids_3' => 144,
));

db_query("SELECT * FROM {node} WHERE nid IN (13, 42, 144)");

 
查询选项(Query options)

db_query()函数(数据库连接的查询方法)的第三个参数是一个选项数组，它指出查询将怎么执行。大多数的查询将使用两个值，其它值大多在内部使用。

target键指出使用的数据库目标，如果没有指定，则使用default。目前还可以使用的有效值是slave，它指出查询将会运行在副SQL服务器上。

fetch键指出如何从查询中获取查询结果。合法的值包括PDO::FETCH_OBJ、PDO::FETCH_ASSOC、PDO::FETCH_NUM、PDO::FETCH_BOTH，或者是一个表示类名的字符串。如果指定类名字符串，每一条记录将会被获取到那个类的对象中。被PDO定义的其它值将会把获取的结果作为标准对象(stdClass object)、关联数组、索引数组、以及关联数组和索引数组。请阅读http://php.net/manual/en/pdostatement.fetch.php以获取更多的细节。缺省为PDO::FETCH_OBJ。

下面的例子将会在一个副服务器上执行查询，并且使用关联数组从查询的结果集中返回记录。

$result = db_query("SELECT nid, title FROM {node}", array(), array(
  'target' => 'slave',
  'fetch' => PDO::FETCH_ASSOC,
));

调用db_query()函数会返回一个结果对象，结果对象包含了返回的所有行和列。下面的例子中，$result变量存储了查询返回的所有行，同时使用了它的fetchAssoc()方法获取一行并存储到$row变量中。

$sql = "SELECT name, quantity FROM goods WHERE vid = :vid";
$result = db_query($sql, array(':vid' => $vid));
if ($result) {
  while ($row = $result->fetchAssoc()) {
    // Do something with:
    // $row['name']
    // $row['quantity']
  }
}