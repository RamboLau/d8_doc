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

一个模块可以定义一个缓存bin，需要在模块目录下的modulename.services.yml文件中定义服务，假定缓存bin的名称为’nameofbin’，其定义的代码如下:

```php
cache.nameofbin:
  class: Drupal\Core\Cache\CacheBackendInterface
  tags:
    - { name: cache.bin }
  factory: cache_factory:get
  arguments: [nameofbin]
```
