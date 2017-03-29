Drupal8在settings.php里通过$databases变量来配置数据库，$databases允许定义多个数据库连接，也支持定义多个target。
 
### 连接键
连接键即一个数据库连接的唯一标识符，默认是default。
 
### 目标(Target)

一个连接键可以有一个或多个target，一个target指一个可以使用的数据库。默认为default，即主服务器。也可以定义一个或多个slave。
当主服务器查询饱和以后，将会尝试使用slave服务器，如果有可用的slave，则会在上面打开连接并运行查询。

### $databases变量语法

$databases是数据库配置变量，它是一个嵌套数组，至少有三个级别。第一级定义数据库的键，第二级定义数据库的目标。每个键值对定义了数据库的连接信息。如:
```php
$databases['default']['default'] = array(
  'driver' => 'mysql',
  'database' => 'drupaldb',
  'username' => 'username',
  'password' => 'secret',
  'host' => 'localhost',
);
```

以上的$databases数组定义了一个连接键("default")和目标("default")。
* driver: 数据库的类型
* database: 连接使用的数据库
* username: 数据库用户名
* password: 数据库密码
* host: 数据库服务器地址。

### master/slave数据库配置

```php
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
```

上面的代码定义了一个”default”的数据库服务器和两个slave数据库服务器。注意slave键是一个数组。这样页面请求将会被随机发送到已定义的服务器之一。

### 外部数据库配置
```php
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
```

上面的配置定义了一个主数据库和名为extra的外部数据库，它使用SQLite数据库类型。注意SQLite的连接信息结构与MySQL不同。

 
### PDO配置
文档见：http://php.net/manual/zh/pdo.setattribute.php

配置例子如：
```php
$databases['default']['default'] = array(
  'driver' => 'mysql',
  'database' => 'drupaldb',
  'username' => 'username',
  'password' => 'secret',
  'host' => 'dbserver1',
  'pdo' => array(ATTR_TIMEOUT => 2.0, MYSQL_ATTR_COMPRESS => 1),
);
```