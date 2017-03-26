配置文档见：https://www.drupal.org/docs/8/api/configuration-api/configuration-api-overview

配置API主要做配置数据的管理，比如网站的站点名称等。

通常配置都是由开发人员创建的，除了超级管理员可以在后台修改配置，其他用户是没有操作权限的。

配置API有两种：

* 1、Config API

* 2、Configuration Entity API

其中，Config API可以认为是一个单独的配置实例，一般只保存简单的Boolean值、数字、字符串等，如站点名称(site name).

Configuration Entity API主要用来存储多个配置集，例如节点类型、视图、词汇和字段等。

