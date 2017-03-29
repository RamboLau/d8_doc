数据库文档见：https://www.drupal.org/docs/7/api/database-api/database-api-overview

API文档见：https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Database%21database.api.php/group/database/8.2.x


Drupal 8的数据库API提供了访问数据库服务的标准抽象层。通常，不需要直接调用数据库API，除非你用它来开发核心API。

这个API在设计时尽可能地考虑了SQL语法和性能，主要功能有：

* 支持多种数据库，如sqlite、pgsql等。
* 允许开发者使用更复杂的功能，如事务。
* 提供一个结构化的接口来动态构造查询。
* 强制安全检测。
* 给模块提供一个简单的接口用以解析和修改站点查询。


Drupal的数据库抽象层是建立在PHP的PDO库之上。PDO提供了访问不同数据库的统一的面向对象的API，但是它并没有抽象出通过不同的数据库而使用不同的SQL专业用语。

 
###1、驱动(Drivers)
因为不同的数据库有不同的特性，Drupal的数据库抽象层需要为每种数据库类型提供了一个驱动器。一个驱动由许多文件组成，它们位于core/lib/drupal/core/database/driver目录下，driver是一个唯一的字符串。在大多数情况下，driver就是小写的数据库名，如"mysql"、"pgsql"或者"mycustomdriver"。

每个驱动由几个类组成，这些类由核心的数据库中的类派生。这些派生的驱动类可以覆写父类的任何方法以支持它们自身的数据库类型。

 
连接(Connections)

一个数据库连接是一个数据库Connection对象，它继承了PDO类。每一个数据库连接都与Drupal中的数所库链接相关。连接对象必须是数据库Connection类的子类如MySQL的连接。

为了访问连接对象你可以使用Database类。

$conn = Database::getConnection($target,$key);

与数据库目标与连接键相关的知识，请阅读数据库配置文档。

访问当前的活动连接可以使用

$conn = Database::getConnection();

注意，在大多数情况下，你不需要直接获取连接对象，可以使用数据库全局函数代替。只要在你要完成复杂的数据库处理时才直接访问连接对象，况且你也不想修改激活的数据库连接。

修改活动连接请使用:

db_set_active($key);

虽然数据库API也为我们提供了常用的全局函数，但是从Drupal 8开始将逐渐放弃那些全局函数，渐渐使用面向对象的方法来代替。

 
查询(Queries)

一个查询是将一个SQL表达式发送给数据库连接。当前的数据库系统支持多种类型的查询:如静态查询、动态查询、插入、更新、删除、合并等等。有些查询直接写成SQL字符串模板(准备查询的表达式)，而其它的查询使用面向对象的方法调用，即查询对象。

 
查询表达式(Statements)

一个表达式对象是一个select查询的结果。它总是Statement类型，或者是它的子类，Statement类扩展至\PDOStatement类并实现StatementInterface接口。

Drupal为所有的查询使用预备的表达式。一个预备的表达式是一个查询模板，在表达式中使用占位符，在执行表达式时将会替换占位符的值。可以将它看成一个SQL函数，在调用它时传递参数。

在PDO中，显示地预备一个表达式对象，然后将特定值绑定到查询中的占位符上并执行。遍历表达式对象得到一个结果集。事实上一个表达式与一个结果集是同意的，但必须在表达式执行之后。

Drupal不会直接暴露预备的表达式。相反，一个模块开发者将使用一个查询对象或者一个一次性的SQL字符串来执行一个查询并返回一个statement对象，statement对象和result对象几乎是同意的。
