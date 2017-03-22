1、Drupal8目录结构
* /core - Drupal 8 的内核目录，所有核心的文件、功能和模块都位于这个目录下，有关 core 目录下的主要目录详见后文说明

* /libraries - 第三方库，例如所见即所得编辑器，默认内核并未包含此目录，如有需要自行创建即可

* /modules - 贡献模块与自定义模块存放位置，类似于 D6, D7 的 sites/all/modules，建议在此目录下新建 contrib 和 custom 目录以分别组织贡献模块和第三方模块

* /profile - 安装配置文件

* /themes - 第三方主题或自定义主题
/sites/[default|all|domain]/[modules|themes] - （在多站点情况下），只在指定站点使用的主题和模块可以放置在此目录结构下，以避免模块和主题出现在所有站点中
/sites/[default|all|domain]/files - （在多站点情况下），此目录放置各种站点文件，如用户上传的文件、站点配置等
 

除以上主要目录结构外，对于开发人员，对 core 目录下的目录结构及作用有所了解是非常必要的。

* /core/asset - Drupal 8 所使用的各种扩展库，如 jQuery, CKEditor, Backbone, Underscore, Modernizer, Normalize CSS 等

* /core/includes - 提供核心API

* /core/lib - Drupal 8 的各种核心类（classes）

* /core/misc - Drupal 8 核心所需要的前端杂项文件，如 js, css, 小图片等等

* /core/modules - Drupal 8 内核模块

* /core/profiles - Drupal 8 内置安装配置文件

* /core/scripts - 开发人员可用的各种命令行脚本

* /core/tests - Drupal 8 测试用相关文件

* /core/themes - Drupal 8 内核主题

* /core/vender - Drupal 8 核心所需要的后端库，如 Symfony2, Twig 等