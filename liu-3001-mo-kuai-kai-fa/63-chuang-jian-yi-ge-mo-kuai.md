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
package: 'Example'
description: 'An example module showing how to define a page to be displayed at a given URL.'
type: module
core: 8.x
```
前三行用于描述模块名、模块描述、所属包(组)，它们将会显示在Drupal 8的模块管理页面。name键指定显示在模块管理页面的名称，description键批定显示在模块管理页面的描述，package键允许对模块进行分组。例如Core包是由Drupal 8核心提供的模块。我们这里使用package:Custom表明此模块属于自定义模块包。