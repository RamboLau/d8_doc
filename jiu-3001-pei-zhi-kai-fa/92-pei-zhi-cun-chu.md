Drupal8的配置信息默认是保存在数据库中，配置文件格式是YAML。

###1、基础配置(hello_world.setting.yml)
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

###2、可选配置
可选配置一般保存在config/optional中，它只在依赖存在的情况下才会生效。

如A模块有一个可选配置依赖于B模块，A模块先安装，当安装B模块时，会自动扫描A模块的config/optional，找到依赖并安装。如果B模块没有安装，那么A模块的可选配置将不会启用。


###3、更新配置
可以使用drush config-import(cim)这个命令把配置更新到数据库中。

首先在settings.php中找到如下配置，如:

```php
$config_directories['sync'] = 'sites/default/files/config_bBkGn-0ZX0fe_fnnlVS3dmxhz7_z6guu819vcYXEpc4NtTBYgr8vB2Tqwp0t8-Oz9j71_los1g/sync';
```

接下来把hello_world.setting.yml放到这个目录下，然后执行drush cim && drush cr命令。访问数据库config，查询配置是否更新成功。
