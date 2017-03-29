Drupal8在settings.php里通过$databases变量来配置数据库，$databases允许定义多个数据库连接，也支持定义多个target。
 
连接键

连接键(connection key)对于一个给定的数据库连接是一个唯一的标识符。连接键对于一个给定的站点必须是唯一的，总是有一个”default”的连接，它是Drupal的主数据库连接。在大多数站点上，只定义了这一个连接。

 
目标(Target)

一个给定的连接键可以有一个或多个目标。一个目标是指一个可以使用的数据库。每个连接键总是可以定义”default”目标。如果请求的目标没有定义，系统将会智能地回退到”default”。

定义数据库目标主要是想使用主/副数据库服务器。”default”目标是主SQL服务器。可以定义一个或多个副服务器(注意，在有些情况，只有副服务器才是有效的替换目标，例如静态查询)。主服务器饱和后，将会尝试使用副SQL服务器。如果有可用的副SQL服务器，将会在上面打开连接并运行查询。如果没有可用的副SQL服务器，查询仍然可以运行在主SQL服务器上。这个回退过程是透明的，因此在写代码时可以考虑使用副服务器，如果它们不可用则自动回退而不需要对代码作任何修改。

 
$databases变量语法

$databases是数据库配置变量，它是一个嵌套数组，至少有三个级别。第一级定义数据库的键。第二级定义数据库的目标。每个键值对定义了数据库的连接信息。请看下面例子:

$databases['default']['default'] = array(
  'driver' => 'mysql',
  'database' => 'drupaldb',
  'username' => 'username',
  'password' => 'secret',
  'host' => 'localhost',
);

以上的$databases数组定义了一个连接键(“default”)和目标(“default”)。这个连接使用了一个MySQL数据库，driver键指出数据库的类型，database键指出连接使用的数据库，username键指出数据库用户名，password键指出数据库密码，host键指出数据库服务器地址。上面的例子是一个典型的只使用单个SQL服务器的例子，对于绝大多数站点这样的配置已经够用了。

对于主/副数据库配置，请看下面的定义:

$databases['default']['default'] = array(
  'driver' => 'mysql',
  'database' => 'drupaldb1',
  'username' => 'username',
  'password' => 'secret',
  'host' => 'dbserver1',
);
$databases['default']['slave'][] = array(
  'driver' => 'mysql',
  'database' => 'drupaldb2',
  'username' => 'username',
  'password' => 'secret',
  'host' => 'dbserver2',
);
$databases['default']['slave'][] = array(
  'driver' => 'mysql',
  'database' => 'drupaldb3',
  'username' => 'username',
  'password' => 'secret',
  'host' => 'dbserver3',
);

上面的代码定义了一个”default”的数据库服务器和两个副数据库服务器。注意slave键是一个数组。如果一个目标被定义成一个连接信息数组，页面请求将会被随机发送到已定义的服务器之一。

$databases['default']['default'] = array(
  'driver' => 'mysql',
  'database' => 'drupaldb1',
  'username' => 'username',
  'password' => 'secret',
  'host' => 'dbserver1',
);
$databases['extra']['default'] = array(
  'driver' => 'sqlite',
  'database' => 'files/extradb.sqlite',
);

上面的配置定义了一个Drupal主数据库和名为”extra”的外部数据库，它使用SQLite数据库类型。注意SQLite的连接信息结构与MySQL不同。每一种数据库驱动的配置信息会依赖于它自身的特性。

 
需求PDO

因为Drupal的数据库抽象层是建立在PHP的PDO类库之上，因此你的主机需要支持PDO扩展，才能运行Drupal。

 
PDO选项

PDO选项和基于特定数据库类型的PDO选项可以在$databases变量中指定，这需要使用pdo键，它的值是一个数据库选项数组。请看下面的例子:

$databases['default']['default'] = array(
  'driver' => 'mysql',
  'database' => 'drupaldb',
  'username' => 'username',
  'password' => 'secret',
  'host' => 'dbserver1',
  'pdo' => array(ATTR_TIMEOUT => 2.0, MYSQL_ATTR_COMPRESS => 1),
);