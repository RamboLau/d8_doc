###1、links.menu.yml
要用到hello_world.links.menu.yml。

在模块的根目录下，创建一个名为hello_world.links.menu.yml的文件，并输入以下内容:

```php
 hello_world.admin:
   title: ‘Hello module settings’
   description: ‘example of how to make an admin settings page link’
   parent: system.admin_config_development
   route_name: hello_world.content
   weight: 100
```

* route_name: 在routing.yml中定义的路由名称
* parent: 描述的是菜单的父链接菜单。这个菜单链接将会在admin>config>development下创建。

清空缓存，看一下效果。

###2、links.action.yml
在模块的根目录下，创建一个名为hello_world.links.action.yml的文件，并输入一下的内容:
```php
hello_world.link_add:
  route_name: hello_world.content.add
  title: 'Add hello world'
  appears_on:
    - hello_world.hello_world
```

* appears_on: 在哪个路由显示

当然也可以定义动态的菜单，如下：
```php
hello_world.content.action:
  route_name: hello_world.content
  title: 'Example dynamic title action'
  weight: -20
  class: '\Drupal\hello_world\Plugin\Menu\LocalAction\CustomLocalAction'
  appears_on:
    - hello_world.content
```

代码如下：
```php
/**
 * @file
 * Contains \Drupal\hello_world\Plugin\Menu\LocalAction\CustomLocalAction.
 */

namespace Drupal\hello_world\Plugin\Menu\LocalAction;

use Drupal\Core\Menu\LocalActionDefault;

/**
 * Defines a local action plugin with a dynamic title.
 */
class CustomLocalAction extends LocalActionDefault {

  /**
   * {@inheritdoc}
   */
  public function getTitle() {
    return $this->t('My @arg action', array('@arg' => 'dynamic-title'));
  }

}
```

###3、links.task.yml
主要是定义tab切换的菜单，如：
```php
example.admin: # The first plugin ID
  title: 'Settings'
  route_name: example.admin
  base_route: example.admin
  
example.admin_3rd_party: # The second plugin ID
  title: 'Third party services'
  route_name: example.admin_3rd_party
  base_route: example.admin
```
