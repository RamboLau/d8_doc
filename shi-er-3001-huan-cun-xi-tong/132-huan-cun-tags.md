缓存tag是一个字符串。缓存tags是以字符串集合进行传递，因此它们的类型提示为string[]。为什么是string[]，因为一个缓存项依赖许多缓存tags。

我们要求，缓存tags必须形如thing:identifier，tag支持下环线，不允许大写。

如：
* node:5 为节点5缓存tag(当它被修改时失效)
* user:3 为用户3缓存tag(当它被修改时失效)
* node_list 为节点实体列出缓存tag(当任何节点被更新、删除或创建时失效)
* config:system.performance 为system.performance配置缓存tag
* library_info 为资源库缓存tag。

###1、核心缓存tags

Drupal的数据管理主要分为三类:
* 实体：缓存tags如<entity type>:<entity ID>
* configuration：缓存tags如config:<configuration name>
* custom(例子library_info)

Drupal自动地为实体&配置提供缓存tags----请看实体基类和配置基类。(所有的实体类型和配置对象继承它们)。使用::getCacheTages()获取它们的缓存tags，例如$node->getCacheTags()，$user->getCacheTags(),$view->getCacheTags()等。EntityTypeInterface::getListCacheTags()用于当创建、更新或删除实体类型时使缓存实体类型的列表失效。

使用cache_tags.invalidator:invalidateTags()让缓存tags失效(或者当你不能注入cache_tags.invalidator服务:Cache::invalidateTages()时)，它接受一个缓存tags集合(string[])。