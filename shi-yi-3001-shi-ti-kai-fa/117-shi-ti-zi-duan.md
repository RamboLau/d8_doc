Drupal8中的实体字段主要有这些：

* boolean 布尔型
* changed 修改日期
* created 创建日期
* decimal 数字
* email 电子邮件
* entity_reference 实体引用
* float 浮点数
* integer 整型
* language 语言
* map 映射
* password 密码
* string_long 长文本
* timestamp 时间戳
* uri 统一资源定位符
* uuid 唯一id
* comment 评论
* datetime 时间日期
* file 文件
* image 图像
* link 链接
* list_float 浮点数列表
* list_integer 整数列表
* list_string 字符串列表
* path 路径
* telephone 电话
* text 文本
* text_long 长文本
* text_with_summary 带摘要的文本


###1、自定义字段类型
#### modules/custom/hello_world/src/Plugin/Field/FieldType/EntityUserAccessField.php

```php
namespace Drupal\MODULENAME\Plugin\Field\FieldType;
 
use Drupal\Core\Field\FieldItemBase;
use Drupal\Core\Field\FieldStorageDefinitionInterface;
use Drupal\Core\TypedData\DataDefinition;
 
/**
 * @FieldType(
 *   id = "entity_user_access",
 *   label = @Translation("Entity User Access"),
 *   description = @Translation("This field stores a reference to a user and a password for this user on the entity."),
 * )
 */
 
class EntityUserAccessField extends FieldItemBase {

  /**
   * {@inheritdoc}
   */
  public static function propertyDefinitions(FieldStorageDefinitionInterface $field_definition) {
    $properties['uid'] = DataDefinition::create('integer')
      ->setLabel(t('User ID Reference'))
      ->setDescription(t('The ID of the referenced user.'))
      ->setSetting('unsigned', TRUE);

    $properties['password'] = DataDefinition::create('string')
      ->setLabel(t('Password'))
      ->setDescription(t('A password saved in plain text. That is not save dude!'));

    // ToDo: Add more Properties.
 
    return $properties;
  }
 
  /**
   * {@inheritdoc}
   */
  public static function schema(FieldStorageDefinitionInterface $field_definition) {
    $columns = array(
      'uid' => array(
        'description' => 'The ID of the referenced user.',
        'type' => 'int',
        'unsigned' => TRUE,
      ),
      'password' => array(
        'description' => 'A plain text password.',
        'type' => 'varchar',
      ),
      'created' => array(
        'description' => 'A timestamp of when this entry has been created.',
        'type' => 'timestamp',
      ),

      // ToDo: Add more columns.
    );
   
    $schema = array(
      'columns' => $columns,
      'indexes' => array(),
      'foreign keys' => array(),
    );

    return $schema;
  }
}
```