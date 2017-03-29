如果你给服务定义了标签，你的服务类必须实现一个相应的接口。Drupal8核心的一些公共例子如下:

* access_check: 指定一个路由检测服务。
* cache.bin:指定一个高速缓存bin服务。
* event_subscriber: 指出一个事件订阅报务。事件订阅可以用于动态路由或路由修改以及其它目的。
* needs_destruction: 如果这个服务已实例化，指出需要在最后调用destruct()方法。
* context_provider:指定一个区块上下文提供者，用于条件区块。必须实现\Drupal\Core\Plugin\Context\ContextProviderInterface。

http_client_middleware:指出该服务提供了一个guzzle中间件。请阅读https://guzzle.readthedocs.org/en/latest/handlers-and-middleware.html以获得更多信息。

