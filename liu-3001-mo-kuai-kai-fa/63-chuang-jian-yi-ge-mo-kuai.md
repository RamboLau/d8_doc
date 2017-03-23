在开始模块开发之前，必须在php.ini中把错误打开，并要学会如何进行错误的调试。
调试一般可以通过\Drupal::log或error_log的输出，前者是记录导watchdog表，后者是记录在php错误日志里，如我们启用了fpm，则会记录在/var/log/php-fpm/www-error.log中。

