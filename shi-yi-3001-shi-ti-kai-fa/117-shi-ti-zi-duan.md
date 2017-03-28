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


###1、自定义字段类型(Field Type)
#### modules/custom/hello_world/src/Plugin/Field/FieldType/EntityUserAccessField.php

字段类型注解以@FieldType开头。
* id: 字段类型的机器名，唯一ID。
* label: 用于用户界面的标签。
* description: 字段描述

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

实现了2个方法：
* propertyDefinitions: 和baseFieldDefinition类似，定义字段的属性。
* schema: 告诉Drupal如何创建字段。 

###2、自定义字段控件(Field Widget)
字段控件用于呈现用户输入表单的数据。如:
* 需要表单输入一个整数，但用户可以通过勾选checkbox以实现输入。
* 你想用自动完成功能输入数据。
* 密码输入有特殊的UI。

#### modules/custom/hello_world/src/Plugin/Field/FieldWidget/EntityUserAccessWidget.php
字段控件注解以@FieldWidget开头：
* id: 控件的机器名
* field_types: 这个字段能使用的字段类型数组
* multiple_values: 缺省为FALSE。如果它是TRUE就可以让你向实体表单提交更多的值。

```php
namespace Drupal\MODULENAME\Plugin\Field\FieldWidget;
 
use Drupal\Core\Field\FieldItemListInterface;
use Drupal\Core\Field\WidgetBase;
use Drupal\Core\Form\FormStateInterface;
 
/**
 * Plugin implementation of the 'entity_user_access_w' widget.
 *
 * @FieldWidget(
 *   id = "entity_user_access_w",
 *   label = @Translation("Entity User Access - Widget"),
 *   description = @Translation("Entity User Access - Widget"),
 *   field_types = {
 *     "entity_user_access",
 *   },
 *   multiple_values = TRUE,
 * )
 */
 
class EntityUserAccessWidget extends WidgetBase {
  /**
   * {@inheritdoc}
   */
  public function formElement(FieldItemListInterface $items, $delta, array $element, array &$form, FormStateInterface $form_state) {
    $element['userlist'] = array(
      '#type' => 'select',
      '#title' => t('User'),
      '#description' => t('Select group members from the list.'),
      '#options' => array(
         0 => t('Anonymous'),
         1 => t('Admin'),
         2 => t('foobar'),
         // This should be implemented in a better way!
       ),
    );

    $element['passwordlist'] = array(
      '#type' => 'password',
      '#title' => t('Password'),
      '#description' => t('Select a password for the user'),
    );
   
    return $element;
  }
}
```
你现在打开带有这个控件的表单，你会看到至少有两个输入字段，一个是用于输入用户名，另一个用于输入密码。如果你想验证表单数据，你需要实现这个控件的验证方法。