缓存的存储被分离到"bins"中，每一个bin包含了各种缓存项。每个bin可以单独地进行配置。

通常我们访问缓存使用
```php
\Drupal::cache($bin = 'default')
```
在cache中可以指定bin的名称，默认为cache.default。

API见：https://api.drupal.org/api/drupal/core!lib!Drupal.php/function/Drupal%3A%3Acache/8.2.x

当你请求一个缓存对象时，你可以在\Drupal::cache()中指定bin的名称。你可以通过从服务容器中获取”cache.nameofbin”来请求一个bin。缺省的bin叫”default”，服务名称为”cache.default”，它用来存储公共的和频繁使用的缓存。

