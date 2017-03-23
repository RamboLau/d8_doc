###4、创建routing.yml文件
Drupal8中的菜单主要由这几个文件构成：

* hello_world.routing.yml 包含URL路径和回调函数的映射关系
* hello_world.links.menu.yml 包含菜单项的结构
* hello_world.links.action.yml 等效Drual7的常量MENU_LOCAL_ACTION
* hello_world.links.task.yml 等效Drupal7的常量MENU_DEFAULT_LOCAL_TASK

在hello_world.routing.yml中添加如下代码：
```php
page_example_description:
  path: '/hello'
  defaults:
    _controller: '\Drupal\hello_world\Controller\HelloController::content'
  requirements:
    _access: 'TRUE'
```

* path: 路由注册的路径，需要以斜线开头
* _controller: 定义路由的路径HelloController的content方法
* requirements: 用户能够访问这个页面所具有的权限

在模块目录中，创建一个符合PSR-4标准的目录结构/src/Controller，并在该目录下创建控制器文件HelloController.php。我们这个模块只是想输出hello world这样的字符串，需要在/src/Controller/HelloController.php文件中输入以下代码:

```php
<?php
/**
 * @file
 * Contains \Drupal\hello_world\Controller\HelloController.
 */

namespace Drupal\hello_world\Controller;

use Drupal\Core\Controller\ControllerBase;
class HelloController extends ControllerBase {
  public function content() {
    return array(
        '#type' => 'markup',
        '#markup' => $this->t('Hello, World!'),
    );
  }
}
```

在url地址栏输入/hello，你将会看到”Hello,World!”这样的信息。