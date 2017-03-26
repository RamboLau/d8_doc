###1、读取配置

```php
// Immutable Config (Read Only)
$config = \Drupal::config('system.performance');
// Mutable Config (Read / Write)
$config = \Drupal::service('config.factory')->getEditable('system.performance');
```

第一个只读，第二个方法可读写。

