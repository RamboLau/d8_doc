如果你给服务定义了标签，你的服务类必须实现一个相应的接口。Drupal8核心的一些公共例子如下:

* access_check: 指定一个路由检测服务。
```php
  access_check.default:
    class: Drupal\Core\Access\DefaultAccessCheck
    tags:
      - { name: access_check, applies_to: _access }
  access_check.entity:
    class: Drupal\Core\Entity\EntityAccessCheck
    tags:
      - { name: access_check, applies_to: _entity_access }
```

* cache.bin:指定一个高速缓存bin服务。
```php
  cache.dynamic_page_cache:
    class: Drupal\Core\Cache\CacheBackendInterface
    tags:
      - { name: cache.bin }
    factory: cache_factory:get
    arguments: [dynamic_page_cache]
```
* event_subscriber: 指出一个事件订阅报务。事件订阅可以用于动态路由或路由修改以及其它目的。
```php
 user_maintenance_mode_subscriber:
    class: Drupal\user\EventSubscriber\MaintenanceModeSubscriber
    arguments: ['@maintenance_mode', '@current_user']
    tags:
      - { name: event_subscriber }
```

* needs_destruction: 如果这个服务已实例化，需要在最后调用destruct()方法。
```php
  path.alias_whitelist:
    class: Drupal\Core\Path\AliasWhitelist
    tags:
      - { name: needs_destruction }
```

* context_provider:指定一个区块上下文提供者，用于条件区块。必须实现\Drupal\Core\Plugin\Context\ContextProviderInterface。
```php
  language.current_language_context:
    class: Drupal\Core\Language\ContextProvider\CurrentLanguageContext
    arguments: ['@language_manager']
    tags:
      - { name: 'context_provider' }
```

* http_client_middleware: 指出该服务提供了一个guzzle中间件。请阅读https://guzzle.readthedocs.org/en/latest/handlers-and-middleware.html以获得更多信息。
```php
  http_client_middleware.webprofiler:
    class: Drupal\webprofiler\Http\HttpClientMiddleware
    tags:
      - { name: http_client_middleware }
```

* backend_overridable: 提供一个标准的重写服务。
```php
 config.storage.active:
    class: Drupal\Core\Config\DatabaseStorage
    arguments: ['@database', 'config']
    public: false
    tags:
      - { name: backend_overridable }
```

* service_collector: 服务收集器 
```php
string_translation:
  class: Drupal\Core\StringTranslation\TranslationManager
  arguments: ['@language_manager']
  calls:
    - [initLanguageManager]
  tags:
    - { name: service_collector, tag: string_translator, call: addTranslator }
```
这里定义了一个服务收集器。在tags定义中定义了tag属性，它会告诉服务收集器什么样的tag名称可以被收集。tags上的call属性告诉Drupal应该为每一项已收集的服务调用TranslationManager::addTranslator()方法一次。
```php
// \Drupal\Core\StringTranslation\TranslationManager:
public function addTranslator(TranslatorInterface $translator, $priority = 0) {
  $this->translators[$priority][] = $translator;
  // Reset sorted translators property to trigger rebuild.
  $this->sortedTranslators = NULL;
  return $this;
}
```