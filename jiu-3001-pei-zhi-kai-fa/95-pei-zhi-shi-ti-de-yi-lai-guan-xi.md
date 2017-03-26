最终实现效果![效果](https://www.drupal.org/files/2016-12-18-002716.png)

###1、定义路由
在example/example.routing.yml中定义四个路由，list,add,edit,delete，代码如下：
```php
entity.example.collection:
  path: '/admin/config/system/example'
  defaults:
    _entity_list: 'example'
    _title: 'Example configuration'
  requirements:
    _permission: 'administer site configuration'

entity.example.add_form:
  path: '/admin/config/system/example/add'
  defaults:
    _entity_form: 'example.add'
    _title: 'Add example'
  requirements:
    _permission: 'administer site configuration'

entity.example.edit_form:
  path: '/admin/config/system/example/{example}'
  defaults:
    _entity_form: 'example.edit'
    _title: 'Edit example'
  requirements:
    _permission: 'administer site configuration'

entity.example.delete_form:
  path: '/admin/config/system/example/{example}/delete'
  defaults:
    _entity_form: 'example.delete'
    _title: 'Delete example'
  requirements:
    _permission: 'administer site configuration'
```

###2、定义菜单
在example/example.links.action.yml定义一个添加按钮。
```php
entity.example.add_form:
  route_name: 'entity.example.add_form'
  title: 'Add example'
  appears_on:
    - entity.example.collection
```

###3、实体类型类

#### example/src/ExampleInterface.php

假设实体里面包含一些属性，需要在接口实现set/get方法。如：

```php
namespace Drupal\example;

use Drupal\Core\Config\Entity\ConfigEntityInterface;

/**
 * Provides an interface defining an Example entity.
 */
interface ExampleInterface extends ConfigEntityInterface {
  // Add get/set methods for your configuration properties here.
}
```

#### example/src/Entity/Example.php
这个文件定义了配置类型的实现类。
```php
namespace Drupal\example\Entity;

use Drupal\Core\Config\Entity\ConfigEntityBase;
use Drupal\example\ExampleInterface;

/**
 * Defines the Example entity.
 *
 * @ConfigEntityType(
 *   id = "example",
 *   label = @Translation("Example"),
 *   handlers = {
 *     "list_builder" = "Drupal\example\Controller\ExampleListBuilder",
 *     "form" = {
 *       "add" = "Drupal\example\Form\ExampleForm",
 *       "edit" = "Drupal\example\Form\ExampleForm",
 *       "delete" = "Drupal\example\Form\ExampleDeleteForm",
 *     }
 *   },
 *   config_prefix = "example",
 *   admin_permission = "administer site configuration",
 *   entity_keys = {
 *     "id" = "id",
 *     "label" = "label",
 *   },
 *   links = {
 *     "edit-form" = "/admin/config/system/example/{example}",
 *     "delete-form" = "/admin/config/system/example/{example}/delete",
 *   }
 * )
 */
class Example extends ConfigEntityBase implements ExampleInterface {

  /**
   * The Example ID.
   *
   * @var string
   */
  public $id;

  /**
   * The Example label.
   *
   * @var string
   */
  public $label;

  // Your specific configuration property get/set methods go here,
  // implementing the interface.
}

```

###4、配置schema

#### example/config/schema/example.schema.yml
在\Drupal\example\Entity\Example中定义的属性添加到这里。

注意：ConfigEntityType注解里的config_prefix字段必须跟schema的prefix一致。比如你定义的config_prefix是这样的：
```php
@ConfigEntityType(
..
... config_prefix = "variable_name" ... 
```
那么schema的格式必须是: example.variable_name.*:...

```php
example.example.*:
  type: config_entity
  label: 'Example config'
  mapping:
    id:
      type: string
      label: 'ID'
    label:
      type: label
      label: 'Label'
```

###5、实体controller

#### example/src/Form/ExampleForm.php
```php
namespace Drupal\example\Form;

use Drupal\Core\Entity\EntityForm;
use Drupal\Core\Entity\Query\QueryFactory;
use Drupal\Core\Form\FormStateInterface;
use Symfony\Component\DependencyInjection\ContainerInterface;

/**
 * Form handler for the Example add and edit forms.
 */
class ExampleForm extends EntityForm {

  /**
   * Constructs an ExampleForm object.
   *
   * @param \Drupal\Core\Entity\Query\QueryFactory $entity_query
   *   The entity query.
   */
  public function __construct(QueryFactory $entity_query) {
    $this->entityQuery = $entity_query;
  }

  /**
   * {@inheritdoc}
   */
  public static function create(ContainerInterface $container) {
    return new static(
      $container->get('entity.query')
    );
  }

  /**
   * {@inheritdoc}
   */
  public function form(array $form, FormStateInterface $form_state) {
    $form = parent::form($form, $form_state);

    $example = $this->entity;

    $form['label'] = array(
      '#type' => 'textfield',
      '#title' => $this->t('Label'),
      '#maxlength' => 255,
      '#default_value' => $example->label(),
      '#description' => $this->t("Label for the Example."),
      '#required' => TRUE,
    );
    $form['id'] = array(
      '#type' => 'machine_name',
      '#default_value' => $example->id(),
      '#machine_name' => array(
        'exists' => array($this, 'exist'),
      ),
      '#disabled' => !$example->isNew(),
    );

    // You will need additional form elements for your custom properties.
    return $form;
  }

  /**
   * {@inheritdoc}
   */
  public function save(array $form, FormStateInterface $form_state) {
    $example = $this->entity;
    $status = $example->save();

    if ($status) {
      drupal_set_message($this->t('Saved the %label Example.', array(
        '%label' => $example->label(),
      )));
    }
    else {
      drupal_set_message($this->t('The %label Example was not saved.', array(
        '%label' => $example->label(),
      )));
    }

    $form_state->setRedirect('entity.example.collection');
  }

  /**
   * Helper function to check whether an Example configuration entity exists.
   */
  public function exist($id) {
    $entity = $this->entityQuery->get('example')
      ->condition('id', $id)
      ->execute();
    return (bool) $entity;
  }

}
```
#### example/src/Controller/ExampleListBuilder.php
```php
namespace Drupal\example\Controller;

use Drupal\Core\Config\Entity\ConfigEntityListBuilder;
use Drupal\Core\Entity\EntityInterface;

/**
 * Provides a listing of Example.
 */
class ExampleListBuilder extends ConfigEntityListBuilder {

  /**
   * {@inheritdoc}
   */
  public function buildHeader() {
    $header['label'] = $this->t('Example');
    $header['id'] = $this->t('Machine name');
    return $header + parent::buildHeader();
  }

  /**
   * {@inheritdoc}
   */
  public function buildRow(EntityInterface $entity) {
    $row['label'] = $this->getLabel($entity);
    $row['id'] = $entity->id();

    // You probably want a few more properties here...

    return $row + parent::buildRow($entity);
  }

}
```

#### example/src/Form/ExampleDeleteForm.php
```php
namespace Drupal\example\Form;

use Drupal\Core\Entity\EntityConfirmFormBase;
use Drupal\Core\Url;
use Drupal\Core\Form\FormStateInterface;

/**
 * Builds the form to delete an Example.
 */

class ExampleDeleteForm extends EntityConfirmFormBase {

  /**
   * {@inheritdoc}
   */
  public function getQuestion() {
    return $this->t('Are you sure you want to delete %name?', array('%name' => $this->entity->label()));
  }

  /**
   * {@inheritdoc}
   */
  public function getCancelUrl() {
    return new Url('entity.example.collection');
  }

  /**
   * {@inheritdoc}
   */
  public function getConfirmText() {
    return $this->t('Delete');
  }

  /**
   * {@inheritdoc}
   */
  public function submitForm(array &$form, FormStateInterface $form_state) {
    $this->entity->delete();
    drupal_set_message($this->t('Category %label has been deleted.', array('%label' => $this->entity->label())));

    $form_state->setRedirectUrl($this->getCancelUrl());
  }
}
```