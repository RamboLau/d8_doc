缓存的存储被分离到"bins"中，每一个bin包含了各种缓存项。每个bin可以单独地进行配置。

通常我们访问缓存使用
```php
\Drupal::cache($bin = 'default')
```
在cache中可以指定bin的名称，默认为cache.default。

API见：https://api.drupal.org/api/drupal/core!lib!Drupal.php/function/Drupal%3A%3Acache/8.2.x

如果我们实现了redis缓存，则可以通过cache.redis来访问。


