###1、实体检测

```php
// 检测对象是否是一个实体的实例
if ($object instanceof \Drupal\Core\Entity\EntityInterface) {

}

// 内容实体实例检测
if ($entity instanceof \Drupal\Core\Entity\ContentEntityInterface){

}

// 获取实体类型
$entity->getEntityTypeId();

// 节点实例检测
if($entity instanceof \Drupal\node\NodeInterface){

}

// 使用entityType获取动态实体类型
$needed_type = ‘node’;
if ($entity->getEntityTypeId() == $needed_type){

}
```

###2、获取实体或实体方法的信息

使用一些方法可以获取实体的信息，如实体的ID，实体bundle，修订ID等等。请看EntityInterface接口获取更多的细节。

```php
// 获取实体id
$entity->id();

// 获取实体bundle
$entity->bundle();

// 检测实体是否为新实体
$entity->isNew();

// 获取实体的标签
$entiry->label();

// 获取实体URI
$entity->uri();

// 创建实体的副本
$duplicate = $entity->createDuplicate();
```

###3、创建实体
```php
$node = entity_create('node', [
 'title' => 'My node',
 'body' => 'The body content. This just works like this due to the new Entity Field API. It will be assigned as the value of the first field item in the default language.'
]);
```

// 你也可以使用静态的创建方法

```php
$node = Node::create(['title' => 'The node title']);
```

// 使用实体管理器(entity manager)
```php
$node = \Drupal::entityTypeManager()->getStorage(‘node’)->create(['type' => 'article', 'title' => 'Another node'));
```

###4、加载实体
```php
// 使用静态方法
$node = Node::load(1);

// 动态实体类型，entity_load()加载新单个实体。
$entity = entity_load($entity_type,$id);

// 使用存储控制器
$entity = \Drupal::entityTypeManager()->getStorage($entity_type)->load(1);

// 加载多个实体使用entity_load_multiple()
$entity = \Drupal::entityTypeManager()->getStorage($entity_type)->loadMultiple(array(1,2,3));
```

为了更新实体，可以先加载它，然后修改，最后保存。

###5、保存实体

```php
// 保存一个实体
$entity->save();
```

这一方法既可用于新实体，也可用于已存在的实体，可以从实体信息中得知实体是否是一个新实体。一般地，对于内容实体，会提供一个实体ID。为了保存一个新实体(例如导入数据)，可以强制使用isNew标志，请看以下代码。

// 下面的代码试图插入一个节点ID为5的节点，如果这个节点ID已存在则会失败
```php
$node->nid->value = 5;

$node->enforceIsNew(TRUE);

$node->save();
```

###6、删除实体
```php
// 删除单个实体
$entity = \Drupal::entityTypeManager()->getStorage(‘node’)->load(1);
$entity->delete();

// 一次删除多个实体
\Drupal::entityTypeManager()->getStorage($entity_type)->delete(array($id1 => $entity1,$id2 => $entity2));
```

###7、访问控制

access()方法可以用于检测谁有权访问实体。这个方法支持很多操作，标准的操作有view、update、delete和create，create有点特殊，请看下面。

访问检测会被转发给访问控制器。

```php
// 检测实体的查看权限，默认会对当前登录用户进行这一检测
if ($entity->access(‘view’)){

}

// 检测一个用户是否可以删除一个实体
if ($entity->access(‘delete’,$account)){

}
```

这个权限只是为了检测某人是否有权创建该实体，开销较大，因此应直接使用访问控制器进行检查。

```php
\Drupal::entity::entityTypeManager()->getAccessController('node')->createAccess('article');
```

如果已经有一个实体，$entity->access('create')也可以执行，它会调用createAccess()方法，同样地其它操作会被转到访问控制器的access()方法上。

注意: 有些资料上使用\Drupal::entityManager()，但已不提倡，并会在9.x中删除。因此请用\Drupa::entityTypeManager()代替\Drupal::entityManager()。