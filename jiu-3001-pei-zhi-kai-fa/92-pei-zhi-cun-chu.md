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

