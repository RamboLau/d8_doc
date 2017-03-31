Drupal8核心提供了几个认证协议，有：

* Cookie
* HTTP Basic

第三方的有：

* OAuth
* Simple OAuth
* IP

### 自定义认证协议

目录结构：modules/custom/test/src/Authentication/Provider/DefaultAuthenticationProvider.php
可以使用
```php
drupal generate:authentication:provider
```
来创建一个认证。

#### 定义服务
```php
  authentication.test:
    class: Drupal\test\Authentication\Provider\DefaultAuthenticationProvider
    arguments: ['@config.factory', '@entity_type.manager']
    tags:
      - { name: authentication_provider, provider_id: default_authentication_provider, priority: 100 }
```

### 实现provider
```php

```