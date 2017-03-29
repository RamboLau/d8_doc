修改一项已有服务，需扩展ServiceProviderBase类并实现alter()方法。

比如在hello_world/src/MyModuleServiceProvider.php中：

```php
namespace Drupal\hello_world;

use Drupal\Core\DependencyInjection\ContainerBuilder;
use Drupal\Core\DependencyInjection\ServiceProviderBase;

/**
 * Modifies the language manager service.
 */

class MyModuleServiceProvider extends ServiceProviderBase {

  /**
   * {@inheritdoc}
   */

  public function alter(ContainerBuilder $container) {
    // Overrides language_manager class to test domain language negotiation.
    $definition = $container->getDefinition('language_manager');
    $definition->setClass('Drupal\language_test\LanguageTestManager');
  }
}
```

这里我们修改了一个language_manager服务。