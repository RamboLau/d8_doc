插件文档见：https://www.drupal.org/docs/8/api/plugin-api/plugin-api-overview

插件即plugin，后文都以插件来指代plugin。

插件是一些可插拔的功能块。Drupal8包含很多不同的插件类型，比如field widget这个插件类型下，又包含了很多插件。

插件通常是在模块中定义，插件系统包含这些概念：

###1、插件类型(plugin type)

插件类型提供一个控制类来定义插件如何被发现和实例化，比如cache backends, image actions, blocks等。

###2、插件发现(plugin discovery)
指如何通过代码去发现插件。

###3、插件工厂(plugin factory)
负责实例化具体的插件。

