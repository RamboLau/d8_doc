在Drupal中INSERT查询操作必须使用一个查询构建对象。不同的数据库对INSERT的操作略有不同，特别是处理LOB(大对象如MySQL的TEXT型)和BLOB(二进制大对象)字段，因此需要数据库抽象层允许特定类型的数据库驱动来实现这些特定于数据库的处理。

INSERT查询操作使用db_insert()函数开始，如下:

$query = db_insert('node', $options);

上面的代码创建一个INSERT查询对象，它将会向节点表插入一条或多条记录。注意表名不需要使用大括号，因为查询构建器会自动地处理它。

INSERT查询对象使用了流式API，除了execute()方法外，其它方法返回查询对象自身，并允许链式调用方法。在大多数情况下，这意味着查询对象根本不需要保存为中间变量。

INSERT查询对象支持多种不同的使用方式以满足不同的需求。通常，需要指定待插入的字段以及它的值，然后执行插入操作。下面是一些推荐的用法。

 
紧凑形式

大多数INSERT查询操作使用紧凑形式:

$nid = db_insert('node')
  ->fields(array(
    'title' => 'Example',
    'uid' => 1,
    'created' => REQUEST_TIME,
  ))
  ->execute();

上面的查询代码与以下SQL语句相同:

INSERT INTO {node} (title, uid, created) VALUES ('Example', 1, 1221717405);

上面的代码将插入操作的各个部份链接在一起。

db_insert('node')

这一行为节点表创建一个新的INSERT查询对象

->fields(array(
    'title' => 'Example',
    'uid' => 1,
    'created' => REQUEST_TIME,
  ))

fields方法接受多种形式的参数，但是最常用的是关联数组。数组的键对于表中的列，键值对于到需插入的列值。其结果是在指定表上执行单个插入操作。

->execute();

execute()方法告诉Drupal运行这个查询。只有调用了这个方法，查询才会真正执行。

与INSERT查询对象上的其它方法返回查询对象自身不同，execute()返回一个自动增加的字段值(hook_schema()中的serial类型值)。在上面的例子中其返回值赋给了$nid。如果没有使用自动增加字段，execute()的返回值是没有定义的并且不应该相信。

一般来说，这种形式的INSERT查询是最常用的

 
冗余形式

$nid = db_insert('node')
  ->fields(array('title', 'uid', 'created'))
  ->values(array(
    'title' => 'Example',
    'uid' => 1,
    'created' => REQUEST_TIME,
  ))
  ->execute();

这种形式与前面的形式相比，虽效果一样，但显得更冗长。

->fields(array('title', 'uid', 'created'))

在调用fields()时其参数使用一个索引数组而不是关联数组，它只是设置是INSERT查询用到的字段(数据表的列)并没有指定字段值。这主要用于运行一次性插入多条记录(后面讲)。

->values(array(
    'title' => 'Example',
    'uid' => 1,
    'created' => REQUEST_TIME,
  ))

调用这个方法指定了一个带有字段名和字段值的关联数组。这个value()方法可以接受一个索引数组作为参数，如果使用索引数组，需注意数组元素的顺序必须与字段顺序相对应。如果使用关联数组，顺序可以随意。通常关联数组具有更高的可读性。

这种查询形式用得较少，因为紧凑形式更简洁。大多数情况下，只有运行multi-insert查询操作时使用。

 
多行插入形式

INSERT查询对象也可以一次性插入多条记录。只须多次调用values()方法将它们加入到查询表达式队列并将它们组合在一起。不过这依赖于具体的数据库能力。对于大多数数据库，将会一次性插入多条记录以提高数据插入速度。在MySQL中，它将会使用MySQL多值插入语法。

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
$query = db_insert('node')->fields(array('title', 'uid', 'created'));
foreach ($values as $record) {
  $query->values($record);
}
$query->execute();

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
