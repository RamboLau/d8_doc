数据库文档见：https://www.drupal.org/docs/7/api/database-api/database-api-overview

API文档见：https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Database%21database.api.php/group/database/8.2.x


Drupal 8的数据库API提供了访问数据库服务的标准抽象层。一般来说，你不要直接调用数据库API，除非你用它来开发核心API。

这个API在设计时尽可能地考虑了SQL语法和性能，并且还具有以下功能:

    轻易支持多个数据库服务器。
    允许开发者使用更复杂的功能，如事务处理。
    提供一个结构化的结口用来动态构造查询。
    强制安全检测和其它好的实践。
    给模块提供一个简单的接口用以解析和修改站点查询。
