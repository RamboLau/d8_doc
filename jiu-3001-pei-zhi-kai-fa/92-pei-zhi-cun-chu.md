Drupal8的配置信息默认是保存在数据库中，配置文件格式是YAML。

以下是创建一个简单配置的示例：

```bash
cd hello_world

mkdir -p config/install

touch config/install/hello_world.settings.yml
```

然后把以下代码写入到hello_world.settings.yml中

```php
some_string: 'Woo kittens!'
some_int: 42
some_bool: true
```

也可以定义一个嵌套的结构，如：

```php
name: thumbnail
label: 'Thumbnail (100x100)'
effects:
  1cfec298-8620-4749-b100-ccb6c4500779:
    id: image_scale
    data:
      width: 100
      height: 100
      upscale: true
    weight: 0
    uuid: 1cfec298-8620-4749-b100-ccb6c4500779
```

也可以动态的去修改setting.yml中的配置。如在hello_world.install中定义如下代码：

```php
/**
 * Implements hook_install().
 */
function modulename_install() {
  // Set default values for config which require dynamic values.
  \Drupal::configFactory()->getEditable('modulename.settings')
    ->set('default_from_address', \Drupal::config('system.site')->get('mail'))
    ->save();
}
```

