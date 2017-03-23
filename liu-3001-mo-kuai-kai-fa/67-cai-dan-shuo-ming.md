* hello_world.links.action.yml 等效Drual7的常量MENU_LOCAL_ACTION
* hello_world.links.task.yml 等效Drupal7的常量MENU_DEFAULT_LOCAL_TASK


###1、在admin/config添加一个菜单
要用到hello_world.links.menu.yml。

在模块的根目录下，创建一个命为hello_world.links.menu.yml的文件，并输入以下内容:

```php
 hello_world.admin:
   title: ‘Hello module settings’
   description: ‘example of how to make an admin settings page link’
   parent: system.admin_config_development
   route_name: hello_world.content
   weight: 100
```

* route_name: 在routing.yml中定义的路由名称
* parent: 描述的是菜单的父链接菜单。这个菜单链接将会在admin>config>development下创建。

清空缓存，看一下效果。

