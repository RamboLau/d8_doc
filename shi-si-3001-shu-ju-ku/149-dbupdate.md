文档见：https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Database%21Connection.php/function/Connection%3A%3Aupdate/8.2.x

在Drupal中更新查询必须使用一个查询建立对象。不同的数据库对大对象LOB(如MySQL中的TEXT)或者BLOB(二进制大对象)的处理有所不同，因此数据库抽象层应该将这些交由相应的数据库驱动类处理。

更新查询使用up_date()方法:

$query = db_update(‘node’,$options);

上面的代码创建了一个update查询对象，将会更新节点表中的一条或多条记录。注意这里的表名不需要使用大括号，查询建立器将会自动处理。

UPDATE查询对象使用流式API。除了execute()方法外，其它方法都返回查询对象自身，以便于链式调用查询方法。这意味着查询对象根本不需要保存为中间变量。

下面有一些UPDATE查询的用法:

/* This is a horrible example as node.status is pulled from node_revision.status table as well, updating it here will do nothing. */
$num_updated = db_update('node')
  ->fields(array(
    'uid' => 5,
    'status' => 1,
  ))
  ->condition('created', REQUEST_TIME - 3600, '>=')
  ->execute();

上面的查询代码将会更新节点表中过去1小时内创建的所有记录，并将它们的uid设为5，status设为1。fields()方法接受一个指定需更新的字段和它的值的关联数组。这与INSERT查询不同，Update::fields()只能接受一个关联数组。因此字段的段序元关紧要。

上面的例子与下面的查询等价:

UPDATE {node} SET uid=5, status=1 WHERE created >= 1221717405;

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
