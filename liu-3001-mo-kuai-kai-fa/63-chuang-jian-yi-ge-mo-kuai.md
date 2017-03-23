在开始模块开发之前，必须在php.ini中把错误打开，并要学会如何进行错误的调试。
调试一般可以通过\Drupal::log或error_log的输出，前者是记录导watchdog表，后者是记录在php错误日志里，如我们启用了fpm，则会记录在/var/log/php-fpm/www-error.log中。

###1、 模块命名
首先你应该给你的模块命名，包括模块的描述与机器名。机器名将会用于模块的相关文件中，注意不要与Drupal核心模块或其它模块重名。

模块命名的基本原则:

* 必段以字母开头。

* 只能包含小写字母与下划线。

* 机器名必须唯一，不能与模块、主题、或配置文件重命。

* 不能使用保留字: src、lib、vendor、assets、css、files、images、js、misc、templates、includes、fixtures、Drupal。

在本例中，我们使用”hello_world”作为模块的机器名。

注意:在模块的机器名中请不要使用大写字母，因为Drupal将无法识别你的HOOK实现。