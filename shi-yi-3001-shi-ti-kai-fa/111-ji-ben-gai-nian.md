实体(entity)API文档见：https://www.drupal.org/docs/8/api/entity-api/introduction-to-entity-api-in-drupal-8

### Drupal8实体
Drupal8包含user、node、comment等实体。每个实体包含若干方法：

如：

* 普遍使用的方法： $entity->id()
* 实体中指定的方法：$node->getTitle()

### 处理器
实体通常需要storage处理器，它支持实体的加载、保存和删除。

另外还有其他一些处理器，比如access control, viewing, listings and forms。

### 最常用的两个实体类型

#### 配置实体(Configuration Entity)
用于配置系统中，支持翻译并提供安装默认值。

#### 内容实体(Content Entity)
由基本字段组成，提供内容修订和翻译支持。