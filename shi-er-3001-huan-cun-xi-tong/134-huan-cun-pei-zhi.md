###1、缓存bin
缓存的存储被分离到"bins"中，每一个bin包含了各种缓存项。每个bin可以单独地进行配置。

通常我们访问缓存使用
```php
\Drupal::cache($bin = 'default')
```
在cache中可以指定bin的名称，默认为cache.default。

API见：https://api.drupal.org/api/drupal/core!lib!Drupal.php/function/Drupal%3A%3Acache/8.2.x

其它常用的缓存bin如下:

* bootstrap: 请求从开始到结束的大部分数据，对数据变化有严格限制，一般是很少失效的。
* render: 包含缓存的HTML字符串，如页面缓存和区块缓存，缓存会变得很大。
* data: 包含根据路径或者上下文而产生的不同数据。
* discovery: 包含缓存的发现数据，如发现了一个插件就要缓存它，避免再次发现它，如发现的YAML数据等。

调用方法为：
```php
\Drupal::cache('bootstrap');
\Drupal::cache('render');
...
```

一个模块可以定义一个缓存bin，需要在模块目录下的modulename.services.yml文件中定义服务，假定缓存bin的名称为'nameofbin'，代码如下:
```php
cache.nameofbin:
  class: Drupal\Core\Cache\CacheBackendInterface
  tags:
    - { name: cache.bin }
  factory: cache_factory:get
  arguments: [nameofbin]
```

###2、配置缓存
Drupal 8的默认缓存数据是存储于数据库中的，这个配置是可以改变的。也可以将整个缓存数据或者是单个缓存bin配置到其它的后端缓存，比如APCu或redis等。

在setttings.php文件中，你可以覆盖默认缓存配置。

如果你的缓存服务实现了\Drupal\Core\CacheBackendInterface接口并名为cache.redis，下面这一行让缓存bin为"cache_render"使用cache.redis缓存服务。

```php
$settings['cache']['bins']['render'] = 'cache.redis';
```

另外，也可以让所有的缓存都使用你提供的缓存服务:

```php
$settings['cache']['default'] = 'cache.redis';
```

 
###3、配置文件缓存

当我们的数据库不够用时，我们无法再将缓存数据缓存到数据库中，另外数据库也比较慢。基于以上原因，我们可以使用文件缓存，Drupal 8已经为我们提供了PHP文件缓存，下面我们来配置它。

```php
$settings['cache']['default'] = 'cache.backend.php';
```