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
  logger.channel.image:
    parent: logger.channel_base
    arguments: ['image']
  logger.channel.cron:
    parent: logger.channel_base
    arguments: ['cron']
  logger.channel.file:
    class: Drupal\Core\Logger\LoggerChannel
    factory: logger.factory:get
    arguments: ['file']
  logger.channel.form:
    parent: logger.channel_base
    arguments: ['form']               
```

* alias: 服务的别名
    arguments:参数用于工厂方法(‘factory_class’)或者是类构造函数(‘class’)。’@’指出其它服务，然后将服务名放在后面，并在它自已的services.yml文件中定义。
    calls:使用setter注入。定义其它方法来调用已经实例化的服务。
    class:服务的类。
    configurator:一个可调用的配置服务。
    factory_class:这个类将会实例化服务的类。
    factory_method:实例化服务类的方法。
    file:在服务加载以前包含的文件。
    parent:一个服务定义的属性能被继承。请看’abstract’。
    properties:属性注入。
    public:设置服务作为公共或私有。私有服务仅仅能被用作其它服务的参数。’true’(default):服务是公共的。’false’:服务是私有的。
    scope:确定被服务容器使用的服务实例的范围。’container’(default):总是使用相同的服务。’prototype’:每次调用服务时都创建一个新实例。’request’:每个请求都创建一个新的实例。
    synchronized:在每个scope改变时服务将会重新配置(从Symfony2.7起开始废弃)。
    synthetic:注入到容器中的服务替换由容器创建的服务。Values:’true’=synthetic服务;’false’(default)=normal服务。
    tags:指出服务分组的名称。Tags用在compiler passes中和收集缓存bins。
