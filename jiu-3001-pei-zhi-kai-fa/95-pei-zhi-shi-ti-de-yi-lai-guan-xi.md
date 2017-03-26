最终实现效果![效果](https://www.drupal.org/files/2016-12-18-002716.png)

###1、定义路由
在example/example.routing.yml中定义四个路由，list,add,edit,delete，代码如下：
```php
entity.example.collection:
  path: '/admin/config/system/example'
  defaults:
    _entity_list: 'example'
    _title: 'Example configuration'
  requirements:
    _permission: 'administer site configuration'

entity.example.add_form:
  path: '/admin/config/system/example/add'
  defaults:
    _entity_form: 'example.add'
    _title: 'Add example'
  requirements:
    _permission: 'administer site configuration'

entity.example.edit_form:
  path: '/admin/config/system/example/{example}'
  defaults:
    _entity_form: 'example.edit'
    _title: 'Edit example'
  requirements:
    _permission: 'administer site configuration'

entity.example.delete_form:
  path: '/admin/config/system/example/{example}/delete'
  defaults:
    _entity_form: 'example.delete'
    _title: 'Delete example'
  requirements:
    _permission: 'administer site configuration'
```

###2、定义菜单
在example/example.links.action.yml定义一个添加按钮。
```php
entity.example.add_form:
  route_name: 'entity.example.add_form'
  title: 'Add example'
  appears_on:
    - entity.example.collection
```

###3、实体类型类

#### example/src/ExampleInterface.php

假设实体里面包含一些属性，需要在接口实现set/get方法。如：

```php
namespace Drupal\example;

use Drupal\Core\Config\Entity\ConfigEntityInterface;

/**
 * Provides an interface defining an Example entity.
 */
interface ExampleInterface extends ConfigEntityInterface {
  // Add get/set methods for your configuration properties here.
}
```

#### example/src/Entity/Example.php
这个文件定义了配置类型的实现类。
```php
namespace Drupal\example\Entity;

use Drupal\Core\Config\Entity\ConfigEntityBase;
use Drupal\example\ExampleInterface;

/**
 * Defines the Example entity.
 *
 * @ConfigEntityType(
 *   id = "example",
 *   label = @Translation("Example"),
 *   handlers = {
 *     "list_builder" = "Drupal\example\Controller\ExampleListBuilder",
 *     "form" = {
 *       "add" = "Drupal\example\Form\ExampleForm",
 *       "edit" = "Drupal\example\Form\ExampleForm",
 *       "delete" = "Drupal\example\Form\ExampleDeleteForm",
 *     }
 *   },
 *   config_prefix = "example",
 *   admin_permission = "administer site configuration",
 *   entity_keys = {
 *     "id" = "id",
 *     "label" = "label",
 *   },
 *   links = {
 *     "edit-form" = "/admin/config/system/example/{example}",
 *     "delete-form" = "/admin/config/system/example/{example}/delete",
 *   }
 * )
 */
class Example extends ConfigEntityBase implements ExampleInterface {

  /**
   * The Example ID.
   *
   * @var string
   */
  public $id;

  /**
   * The Example label.
   *
   * @var string
   */
  public $label;

  // Your specific configuration property get/set methods go here,
  // implementing the interface.
}

```

###4、配置schema

#### example/config/schema/example.schema.yml
在\Drupal\example\Entity\Example中定义的属性添加到这里。

注意：ConfigEntityType注解里的config_prefix字段必须跟schema的prefix一致。
```php
example.example.*:
  type: config_entity
  label: 'Example config'
  mapping:
    id:
      type: string
      label: 'ID'
    label:
      type: label
      label: 'Label'
```
