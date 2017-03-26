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
   String: 字符串类型必须使用双引号例如"foo"。如果字符串中包含双引号则需再使用一对双引号以转义，如 "The  ""On""  Value"。
   Numbers: 不能使用引号例如21，使用引号将被解析成字符串。
   Booleans: 不能使用引号例如TRUE或者FALSE，使用引号将被解析成字符串。
   Lists: 列表数组，如:
   > base = {
     "node",
     "foo",
   }
   请注意列表中的最后一个元素后的逗号，它有助于防止解析错误。
   Maps: 数据映射，形如数组。如:
   > edit = {
      "editor" = "direct"，
   }
   允许使用常量