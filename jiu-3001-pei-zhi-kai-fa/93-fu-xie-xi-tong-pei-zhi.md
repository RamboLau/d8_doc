###1、覆写Global

Drupal8可以使用$config这个全局变量来访问配置。

通常通过Drupal\Core\Config\ConfigFactory::get()这个API来获取配置的值。

比如，我们获取网站维护的message:

```php
$message = \Drupal::config('system.maintenance')->get('message');
```

可以在setting.php中直接来覆写这个变量：
```php
$config['system.maintenance']['message'] = 'Sorry, our site is down now.';
```