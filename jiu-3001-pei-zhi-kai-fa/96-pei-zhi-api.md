###1、读取配置

```php
// Immutable Config (Read Only)
$config = \Drupal::config('system.performance');
// Mutable Config (Read / Write)
$config = \Drupal::service('config.factory')->getEditable('system.performance');
```

第一个只读，第二个方法可读写。

配置读取一般使用get方法。如：

```php
$config = \Drupal::config('system.maintenance');
$message = $config->get('message');
```
也可以改为链式写法：

```php
$message = \Drupal::config('system.maintenance')->get('message');
```

读取一个嵌套配置，使用.号连接起来。如：
```php
$enabled = \Drupal::config('system.performance')->get('cache.page.enabled');
```

###2、写入配置
通常使用getEditable这个方法从\Drupal\Core\Config\Config中获取。
如：
```php
\Drupal::service('config.factory')->getEditable('system.performance');
```

一般使用set和save方法来变更配置。如：
```php
$config = \Drupal::service('config.factory')->getEditable('system.performance');

// Set a scalar value.
$config->set('cache.page.enabled', 1);

// Set an array of values.
$page_cache_data = array('enabled' => 1, 'max_age' => 5);
$config->set('cache.page', $page_cache_data);

// Save your data to the file system.
$config->save();
```

也可以写成链式：
```php
\Drupal::service('config.factory')->getEditable('system.performance')->set('cache.page.enabled', 1)->save();
```

如果要覆盖所有的配置数据，可以使用setData方法，代码如下:
```php
// Set all values.
\Drupal::service('config.factory')->getEditable('system.performance')->setData(array(
    'cache' => array(
      'page' => array(
        'enabled' => '0',
        'max_age' => '0',
      ),
    ),
    'preprocess' => array(
      'css' => '0',
      'js' => '0',
    ),
    'response' => array(
      'gzip' => '0',
    ),
  ))
  ->save();
```
注意：setData会完全改变配置数据结构，务必更改的时候对应好键值。

###3、删除配置

#### 删除一个单独的配置
代码如下：
```php
$config = \Drupal::service('config.factory')->getEditable('system.performance');
$config->clear('cache.page.max_age')->save();
$page_cache_data = $config->get('cache.page');
```
这时候打印$page_cache_data，会发现max_age已经被删除了。

#### 删除所有配置
通常使用delete方法，代码如下：
```php
\Drupal::service('config.factory')->getEditable('system.performance')->delete();
```