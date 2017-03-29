假定模块名为example，服务则是在example.services.yml中定义的。当这个文件被放置到模块的根目录下时，它将会自动地被Drupal检测到。

下面是一个example.services.yml文件的例子:
```php
services:
  # Defines a simple service of which requires no parameter for its constructor.
  example.simple:
    class: Drupal\Example\Simple

  # Defines a service which requires the module_handler for its constructor.
  example.with_module_handler
    class: Drupal\Example\WithModuleHandler
    arguments: ['@module_handler']
```

在核心模块可以找到大把的例子。


### 服务的属性

* abstract: 取值true/false, true定义一个抽象的服务，false定义一个可实例化的服务。
如：
```php
 logger.channel_base:
    abstract: true
    class: Drupal\Core\Logger\LoggerChannel
    factory: logger.factory:get
  logger.channel.default:
    parent: logger.channel_base
    arguments: ['system']
  logger.channel.php:
    parent: logger.channel_base
    arguments: ['php']             
```
上面的例子我们定义了一个抽象的服务logger.channel_base，下面的default、php等服务都继承了这个抽象服务。

* alias: 服务的别名
* arguments: 参数，@指其他服务
* calls: 使用setter注入，定义其它方法来调用已经实例化的服务
* class: 服务的类
* configurator: 一个可调用的配置服务
* factory_class: 服务的工厂类
* factory_method: 实例化服务工厂类的方法
* file: 在服务加载以前包含的文件
* parent: 一个服务定义的属性能被继承。请看'abstract'。
* properties: 属性注入。
* public: 设置服务为公共或私有。私有服务仅仅能被用作其它服务的参数。true(default): 服务是公共的，false: 服务是私有的
* scope: 确定被服务容器使用实例的范围。'container'(default): 每次从容器中请求该实例都使用同一实例。'prototype': 每次调用服务时都创建一个新实例。'request':每个请求都创建一个新的实例。
* synthetic: 注入到容器中的服务替换由容器创建的服务。true:synthetic服务;false(default):normal服务。
* tags: 指该服务用于特定的应用。
如：
```php
 cache_context.ip:
    class: Drupal\Core\Cache\Context\IpCacheContext
    arguments: ['@request_stack']
    tags:
      - { name: cache.context }
```
Drupal8运行的时候，cache会找到所有实现了cache.context的服务，并把自动注册。