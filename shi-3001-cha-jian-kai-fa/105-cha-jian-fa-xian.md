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
