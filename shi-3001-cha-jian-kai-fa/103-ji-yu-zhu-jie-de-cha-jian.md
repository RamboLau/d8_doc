Drupal8中要声明一个插件，通常是通过注解来完成的。

###1、简单示例
如在core/modules/user/src/Plugin/Validation/Constraint/UserNameUnique.php中可以找到如下代码：

```php
/**
 * Checks if a user name is unique on the site.
 *
 * @Constraint(
 *   id = "UserNameUnique",
 *   label = @Translation("User name unique", context = "Validation"),
 * )
 */
class UserNameUnique extends Constraint {
...
}
```

这里定义了一个Constraint插件，注解包含两个字段：
* id 插件ID
* label 标签

###2、为什么要使用注解
主要是为了优化Drupal的内存使用，在Drupal8之前，我们一般是在每个module文件中定义，PHP加载的时候会把所有文件加载进内存，这样效率比较低下。而通过注解，只需要把注解片段加载进内存，从而使内存使用最小化。

###3、注解的语法
注解使用固定的键值来描述结构化数据，并支持嵌套。

注解语法见：http://doctrine-orm.readthedocs.io/en/latest/reference/annotations-reference.html?highlight=annotations

* id: 插件的机器名，也是唯一ID
* 必须使用双引号，不要使用单引号，单引号会抛出异常
* 常用的数据类型：
   * String: 字符串类型必须使用双引号例如"foo"。如果字符串中包含双引号则需再使用一对双引号以转义，如 "The  ""On""  Value"。
   * Numbers: 不能使用引号例如21，使用引号将被解析成字符串。
   * Booleans: 不能使用引号例如TRUE或者FALSE，使用引号将被解析成字符串。
   * Lists: 列表数组，如:
   > base = {
     "node",
     "foo",
   }
   请注意列表中的最后一个元素后的逗号，它有助于防止解析错误。
   * Maps: 数据映射，形如数组。如:
   > edit = {
      "editor" = "direct"，
   }
   * 允许使用常量
   
###4、自定义注解类
在 core/modules/text/src/Plugin/Field/FieldFormatter/TextTrimmedFormatter.php 中可以看到使用了一个自定义的注解类FieldFormatter。
```php
<?php

namespace Drupal\text\Plugin\Field\FieldFormatter;

use Drupal\Core\Field\FormatterBase;
use Drupal\Core\Field\FieldItemListInterface;
use Drupal\Core\Form\FormStateInterface;

/**
 * Plugin implementation of the 'text_trimmed' formatter.
 *
 * Note: This class also contains the implementations used by the
 * 'text_summary_or_trimmed' formatter.
 *
 * @see \Drupal\text\Field\Formatter\TextSummaryOrTrimmedFormatter
 *
 * @FieldFormatter(
 *   id = "text_trimmed",
 *   label = @Translation("Trimmed"),
 *   field_types = {
 *     "text",
 *     "text_long",
 *     "text_with_summary"
 *   },
 *   quickedit = {
 *     "editor" = "form"
 *   }
 * )
 */
class TextTrimmedFormatter extends FormatterBase {
...
}
```

###5、在插件类型中使用注解
如何在插件类型中使用注解呢？只要扩展DefaultPlguinManager并使用AnnotatedClassDiscovery.

DefaultPluginManager构造函数的第一个参数是指定插件的搜索路径，最后一个参数是上面定义的自定义注释类。在这个例子中，将会在hello_world/src/Plugin/Field/FieldFormatter目录中搜索插件。

```php
use Drupal\Component\Plugin\Factory\DefaultFactory;
use Drupal\Core\Cache\CacheBackendInterface;
use Drupal\Core\Extension\ModuleHandlerInterface;
use Drupal\Core\Plugin\DefaultPluginManager;
  
class FormatterPluginManager extends DefaultPluginManager {
    
  /**
   * Constructs a FormatterPluginManager object.
   */
  public function __construct(\Traversable $namespaces, CacheBackendInterface $cache_backend, ModuleHandlerInterface $module_handler, FieldTypePluginManagerInterface $field_type_manager) {
    parent::__construct('Plugin/Field/FieldFormatter', $namespaces, $module_handler, 'Drupal\Core\Field\FormatterInterface', 'Drupal\Core\Field\Annotation\FieldFormatter');
   
    $this->setCacheBackend($cache_backend, 'field_formatter_types_plugins');
    $this->alterInfo('field_formatter_info');
    $this->fieldTypeManager = $field_type_manager;
  }
}
```

而注入的命名空间来自于依赖注入容器，如FieldBundle:
```php
/**
 * @file
 * Contains Drupal\field\FieldBundle.
 */

namespace Drupal\field;

use Symfony\Component\DependencyInjection\ContainerBuilder;
use Symfony\Component\HttpKernel\Bundle\Bundle;

/**
 * Field dependency injection container.
 */
class FieldBundle extends Bundle {

  /**
   * Overrides Symfony\Component\HttpKernel\Bundle\Bundle::build().
   */
  public function build(ContainerBuilder $container) {
    // Register the plugin managers for our plugin types with the dependency injection container.
    $container->register('plugin.manager.field.widget', 'Drupal\field\Plugin\Type\Widget\WidgetPluginManager')
      ->addArgument('%container.namespaces%');
    $container->register('plugin.manager.field.formatter', 'Drupal\field\Plugin\Type\Formatter\FormatterPluginManager')
      ->addArgument('%container.namespaces%');
  }

}
```