通常可以通过

```php
drupal generate:plugin:rest:resource
```
来创建一个接口。

###1、注解
```php
 * @RestResource(
 *   id = "default_rest_resource",
 *   label = @Translation("Default rest resource"),
 *   uri_paths = {
 *     "canonical" = "/tests"
 *   }
 * )
```