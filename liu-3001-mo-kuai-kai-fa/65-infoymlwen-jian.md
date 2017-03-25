### 创建info.yml文件
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
description: Creates a page showing "Hello World"
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

* hidden:true 在站点Extend页面(/admin/modules)的模块列表中隐藏你的模块。如果模块仅仅包含一个测试或作为一个开发例子，你发现使用这个键是非常有用的。

也可以在settings.php文件中添加

```php
$settings['extension_discovery_scan_tests'] = TRUE
```
以显示模块。

