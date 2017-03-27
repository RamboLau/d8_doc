插件发现是指Drupal系统发现某种插件的处理过程。每一种插件类型都需要实现插件发现方法，在插件管理器中已经做过解释。
插件发现组件实现了DiscoveryInterface接口，该接口定义了任何插件发现的类必须具有的方法。

在core/lib/Drupal/Component/Plugin/Discovery/DiscoveryInterface.php中可以看到如下代码：

```php
<?php

namespace Drupal\Component\Plugin\Discovery;

/**
 * An interface defining the minimum requirements of building a plugin
 * discovery component.
 *
 * @ingroup plugin_api
 */
interface DiscoveryInterface {

  /**
   * Gets a specific plugin definition.
   *
   * @param string $plugin_id
   *   A plugin id.
   * @param bool $exception_on_invalid
   *   (optional) If TRUE, an invalid plugin ID will throw an exception.
   *
   * @return mixed
   *   A plugin definition, or NULL if the plugin ID is invalid and
   *   $exception_on_invalid is FALSE.
   *
   * @throws \Drupal\Component\Plugin\Exception\PluginNotFoundException
   *   Thrown if $plugin_id is invalid and $exception_on_invalid is TRUE.
   */
  public function getDefinition($plugin_id, $exception_on_invalid = TRUE);

  /**
   * Gets the definition of all plugins for this type.
   *
   * @return mixed[]
   *   An array of plugin definitions (empty array if no definitions were
   *   found). Keys are plugin IDs.
   */
  public function getDefinitions();

  /**
   * Indicates if a specific plugin definition exists.
   *
   * @param string $plugin_id
   *   A plugin ID.
   *
   * @return bool
   *   TRUE if the definition exists, FALSE otherwise.
   */
  public function hasDefinition($plugin_id);

}
```

* getDefinition: 返回指定的插件定义方法
* getDefinitions: 返回插件定义的集合
* hasDefinition: 指出插件定义是否存在

Drupal8还包含其他四种不同的插件发现类型：
####1.StaticDiscovery

StaticDiscovery允许在插件发现类中直接注册插件。在类中的protected变量$definitions存储了所有通过方法setDefinition()注册的插件定义。任何通过这种方法定义的插件可以被唤醒。

####2.HookDiscovery

HookDiscovery类允许使用Drupal的hook_comonent_info()/hook_comonent_info_alter()进行插件发现。使用这种插件发现机制，插件管理器将会调用hook来返回可用的插件列表。

####3.AnnotatedClassDiscovery

AnnotatedClassDiscovery即注解类发现，该发现机制允许你在定义插件的类的文件中插入注解，在注解中按照相关的格式定义插件注解类，AnnotatedClassDiscovery会对这些文件进行扫描，从而发现插件。

####4.YamlDiscovery

YamlDiscovery允许你在yaml文件中定义插件类型。Drupal核心主要将它用在local task和local action上。