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

