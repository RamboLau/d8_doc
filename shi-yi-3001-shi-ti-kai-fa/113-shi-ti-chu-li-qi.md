处理器(Handlers)用于响应实体的一些操作。

实体处理器可以通过entity_type.manager service来访问。

 
存储(Storage)

存储处理器实现了EntityStorageInterface接口并且缺省为ContentEntityStorageBase类，在这个类中提供了标准的方法以实现CRUD操作

 
访问(Access)

访问处理器实现了EntityAccessControlHandlerInterface接口，默认使用EntityAccessControlHandler类。

 
List Builder

列表建立器实现了EntityListBuilderInterface接口，默认使用EntityListBuilder类。

 
View Builder

View Builder实现了EntityViewBuilderInterface接口，默认使用EntityViewBuilder类。

 
表单(Form)

表单处理器实现了EntityFormInterface接口。因为EntityForm类已经实现了这个接口，你可以直接对该类进行扩展，以实现如add、edit和delete等操作。然后你可以通过routing.yml文件访问这些操作。请看Drupal配置实体的例子，如src/Entity/Robot.php。

$storage = \Drupal::entityManager()->getStorage('node');

$storage->load(1);
$storage->loadMultiple(array(1, 2, 3));
// Equaivalent to $node->save().
$storage->save($node);
$new_node = $storage->create(array('title' => 'My awesome node'));