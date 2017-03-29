Drupal8提供了两种方式：

* 依赖注入
* 全局函数访问

###1、依赖注入
依赖注入是访问服务的最佳实践。

在服务中显示地传递一个依赖的对象叫做依赖注入。在有些情况下，依赖被显示地传入构造函数，例如，路由访问检测在服务创建时获取当前用户注入，并在检查访问时传递当前请求。你也可以使用setter方法设置一个依赖。

###2、全局函数访问
全局Drupal类提供了静态方法来访问几种公共服务。例如，Drupal::moduleHandler()将返回模块处理服务，Drupal::translation()将返回字符串翻译服务。如果没有指定方法来访问你想访问的服务，你可以使用Drupal::service()方法来获取已经定义的服务。

如访问数据库服务通过专门的\Drupal::database()。

```php
// Returns a Drupal\Core\Database\Connection object.
$connection = \Drupal::database();
$result = $connection->select('node', 'n')
  ->fields('n', array('nid'))
  ->execute();
```

通过\Drupal::service()访问日期服务:
```php
// Returns a Drupal\Core\Datetime\Date object.
$date = \Drupal::service('date');
```
