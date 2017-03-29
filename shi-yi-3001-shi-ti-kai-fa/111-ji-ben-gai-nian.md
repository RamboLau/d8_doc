实体API：https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Entity%21entity.api.php/group/entity_api/8.2.x

实体(entity)文档见：https://www.drupal.org/docs/8/api/entity-api/introduction-to-entity-api-in-drupal-8

### Drupal8实体
Drupal8常见的实体如下：

* 节点(node)
* 评论(comment)
* 分类术语(taxonomy)
* 用户(user)
* 配置实体(configuration)

每个实体包含若干方法：

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

### Bundles
Bundles是一个信息容器，包含了字段或配置的定义。如：

#### 内容实体(Content Entity Types)

* 1、节点(Node), Bundles如内容类型(Content Types)，默认包含Article和Basic page

* 2、分类(Taxonomy), Bundles如词汇(Vocabulary)，如词汇A，词汇B

* 3、区块(Block), Bundles如自定义区块类型，如区块类型A，区块类型B

* 4、用户(User), 没有Bundles

#### 配置实体(Configuration Entity Types)
暂无Bundles