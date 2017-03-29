缓存tag是一个字符串。缓存tags是以字符串集合进行传递，因此它们的类型提示为string[]。为什么是string[]，因为一个缓存项依赖许多缓存tags。

我们要求，缓存tags必须形如thing:identifier，tag支持下环线，不允许有大写。

如：
* node:5 为节点5缓存tag(当它被修改时失效)
* user:3 为用户3缓存tag(当它被修改时失效)
* node_list 为节点实体列出缓存tag(当任何节点被更新、删除或创建时失效)
* config:system.performance 为system.performance配置缓存tag
* library_info 为资源库缓存tag。
