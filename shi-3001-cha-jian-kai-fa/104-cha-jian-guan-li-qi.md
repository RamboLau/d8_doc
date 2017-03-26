插件管理器是一个主控类，它定义了如何发现和实例化某种类型的插件。这个类可以由任何模块调用。

###1、定义插件管理器

要定义一个插件管理器，你需要定义一个discovery方法，还需要定义一个工厂类(factory)。推荐使用DefaultPluginManager作为基类，因为它已经内建了语言缓存、基于注解的插件发现机制以及其它一些基本功能。

###2、定义服务

插件管理器应该定义成一项服务，最好在服务名前加上plugin.manager，这样便于阅读。如hello_world.services.yml定义了一项插件管理服务:

```php
services:
  plugin.manager.archiver:
    class: Drupal\Core\Archiver\ArchiverManager
    parent: default_plugin_manager
```

代码如下：
```php
namespace Drupal\Core\Archiver;

use Drupal\Component\Plugin\Factory\DefaultFactory;
use Drupal\Core\Cache\CacheBackendInterface;
use Drupal\Core\Extension\ModuleHandlerInterface;
use Drupal\Core\Plugin\DefaultPluginManager;

/**
 * Provides an Archiver plugin manager.
 *
 * @see \Drupal\Core\Archiver\Annotation\Archiver
 * @see \Drupal\Core\Archiver\ArchiverInterface
 * @see plugin_api
 */
class ArchiverManager extends DefaultPluginManager {

  /**
   * Constructs a ArchiverManager object.
   *
   * @param \Traversable $namespaces
   *   An object that implements \Traversable which contains the root paths
   *   keyed by the corresponding namespace to look for plugin implementations.
   * @param \Drupal\Core\Cache\CacheBackendInterface $cache_backend
   *   Cache backend instance to use.
   * @param \Drupal\Core\Extension\ModuleHandlerInterface $module_handler
   *   The module handler to invoke the alter hook with.
   */
  public function __construct(\Traversable $namespaces, CacheBackendInterface $cache_backend, ModuleHandlerInterface $module_handler) {
    parent::__construct(
      'Plugin/Archiver',
      $namespaces,
      $module_handler,
      'Drupal\Core\Archiver\ArchiverInterface',
      'Drupal\Core\Archiver\Annotation\Archiver'
    );
    $this->alterInfo('archiver_info');
    $this->setCacheBackend($cache_backend, 'archiver_info_plugins');
  }

}
```
上面的代码告诉系统，我们将寻找任何可用的Plugin/Archiver注解类，使用它来发现插件，并支持插件派生。其它模块可以通过hook_archiver_info_alter()来修改插件定义。系统使用缓存id为archiver_info_plugins对该定义进行缓存。

###3、发现封装器

发现封装器是对一个定义发现的类的封装，它实际上也是一个类，它实现了所有相同的方法，并可以提供其它的方法以作一些额外的处理。使用这种机制实际上是对Drupal的插件机制的扩展，这样可以使它更易扩展，更加灵活。例如扩展至DefaultPluginManager类可以使用下面的方法来派生发现。