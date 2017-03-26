Drupal8中要声明一个插件，通常是通过注解来完成的。

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