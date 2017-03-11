有时站点需要处于维护模式，如Drupal 8 核心升级，站点出现错误，有大量工作需要维护，数据库维护忧化，手动运行cron等等。

点击Manage->Configuration->Maintenance mode 出维护模式页面，勾选 将站点置于维护模式复选框，开启站点维护模式，开启站点维护后，具有”Access site in maintenance mode”的权限的用户仍然可以访问站点，注册用户可以输入用户登录URL登录系统。其它的访客只能看到维护提示消息，维护提示消息在下面的文本域中设置。