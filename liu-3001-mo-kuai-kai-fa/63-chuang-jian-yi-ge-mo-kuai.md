在开始模块开发之前，必须在php.ini中把错误打开，并要学会如何进行错误的调试。
调试一般可以通过\Drupal::log或error_log的输出，前者是记录导watchdog表，后者是记录在php错误日志里，如我们启用了fpm，则会记录在/var/log/php-fpm/www-error.log中。

###1、 模块命名
首先你应该给你的模块命名，包括模块的描述与机器名。机器名将会用于模块的相关文件中，注意不要与Drupal核心模块或其它模块重名。

模块命名的基本原则:

* 必段以字母开头。

* 只能包含小写字母与下划线。

* 机器名必须唯一，不能与模块、主题、或配置文件重命。

* 不能使用保留字: src、lib、vendor、assets、css、files、images、js、misc、templates、includes、fixtures、Drupal。

这里，我们使用”hello_world”作为模块的机器名。

###2、创建目录
模块的机器名为”hello_world”，因此我们需要为模块创建hello_world目录，在drupal的安装目录/modules/custom/hello_world，或者/sites/all/modules/custom/hello_world。如果没有custom目录，请创建之。注意模块的目录名与机器名可以不同，比如我们使用HelloWorld作为模块的目录名，但请记住，模块的代码文件中必须使用模块机器名。**但是为了保证代码的可读性，我们规定模块目录名必须和模块机器名一样。**

以前的版本中自定义模块目录是放在/sites/all/modules下的，核心模块放在/modules。在Drupal 8 中，所有的核心模块、库等都放在/core目录中。贡献模块与自定义模块放在/modules目录中，但你也可以放在/sites/all/modules目录中，其结果是一样的。

我们这个模块现在还没有任何功能，我们需要先建立一个.info.yml文件，以使Drupal 8能发现它。

###3、创建info.yml文件
* 1、创建hello_world.info.yml文件
* 2、增加以下代码
```php
name: 'hello_world'
package: 'Custom'
description: 'An example module showing how to define a page to be displayed at a given URL.'
type: module
core: 8.x
```
前三行用于描述模块名、模块描述、所属包(组)，它们将会显示在Drupal 8的模块管理页面。name指定显示在模块管理页面的名称，description指定显示在模块管理页面的描述，package允许对模块进行分组。例如Core包是由Drupal 8核心提供的模块。我们这里使用package:Custom表明此模块属于自定义模块包。type作用是指定扩展的类型，可能值为module、theme或profile。

info.yml里还有其他的一些属性：
```php
name: hello_world
description: Creates a page showing “Hello World”
package: Custom
type: module
core: 8.x
dependencies:
- datetime: datetime
- link: link
- drupal: views
test_dependencies:
  - drupal: image

configure: hello_world.settings
hidden: true

# Note: do not add the ‘version’ property yourself!
# It will be added automatically by the packager on drupal.org

version:1.0
```

* dependencies: 列出你的模块依赖的模块。其格式为{project}:{module}，{project}是出现在Drupal.org项目url中的项目名称，例如drupal.org/project/views，{module}是模块的机器名。

* test_dependencies: 测试的依赖模块。testbot会对整个模块的依赖关系进行测试。如果通过测试依赖关系是正确的、完整的，应该将其移动dependencies中。在上面的例子中，在测试期间testbot会包含image模块。但站点安装”Hello World Module”模块时不会要求安装image模块。测试依赖的命名规则与依赖的命名规则相同。
testbot测试见 https://www.drupal.org/docs/8/phpunit/running-phpunit-tests。

* configure: 如果你的模块提供了一个配置表单，在这里可以指定配置的路由。当用户展开模块细节时，它将会在Extend页(/admin/modules)显示一个链接。

* hidden:true 在站点Extend页面(/admin/modules)的模块列表中隐藏你的模块。如果模块仅仅包含一个测试或作为一个开发例子，你发现使用这个键是非常有用的。也可以在settings.php文件中添加
```php
$settings['extension_discovery_scan_tests'] = TRUE
```
以显示模块。

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

在url地址栏输入/hello，你将会看到”Hello,World!”这样的信息