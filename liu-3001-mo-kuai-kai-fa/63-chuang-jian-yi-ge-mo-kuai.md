在开始模块开发之前，必须在php.ini中把错误打开，并要学会如何进行错误的调试。
调试一般可以通过\Drupal::log或error_log的输出，前者是记录导watchdog表，后者是记录在php错误日志里，如我们启用了fpm，则会记录在/var/log/php-fpm/www-error.log中。

###、 模块命名
首先你应该给你的模块命名，包括模块的描述与机器名。机器名将会用于模块的相关文件中，注意不要与Drupal核心模块或其它模块重名。

模块命名的基本原则:

* 必段以字母开头。

* 只能包含小写字母与下划线。

* 机器名必须唯一，不能与模块、主题、或配置文件重命。

* 不能使用保留字: src、lib、vendor、assets、css、files、images、js、misc、templates、includes、fixtures、Drupal。

这里，我们使用”hello_world”作为模块的机器名。





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