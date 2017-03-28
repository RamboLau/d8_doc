本节我们来创建一个名为"contact"的内容实体。可以通过drupalconsole来创建，如：

```php
jerry@mac:~/Sites/drupal8 > drupal list | grep entity
 entity
  entity:debug                            Debug entities available in the system
  entity:delete                           Delete an specific entity
  generate:entity:bundle (geb)            Generate a new content type (node / entity bundle)
  generate:entity:config (gecg)           Generate a new config entity
  generate:entity:content (gect)          Generate a new content entity
```

###1、创建模块
#### content_entity_example.info.yml

```php
name: Content Entity Example
type: module
description: 'Provides ContentEntityExampleContact entity.'
package: Example modules
core: 8.x
# These modules are required by the tests, must be available at bootstrap time
dependencies:
  - options
  - entity_reference
  - examples
```

###2、设置路由
Routing

#### content_entity_example.routing.yml

```php
# This file brings everything together. Very nifty!

# Route name can be used in several places; e.g. links, redirects, and local
# actions.
entity.content_entity_example_contact.canonical:
  path: '/content_entity_example_contact/{content_entity_example_contact}'
  defaults:
  # Calls the view controller, defined in the annotation of the contact entity
    _entity_view: 'content_entity_example_contact'
    _title: 'Contact Content'
  requirements:
  # Calls the access controller of the entity, $operation 'view'
    _entity_access: 'content_entity_example_contact.view'

entity.content_entity_example_contact.collection:
  path: '/content_entity_example_contact/list'
  defaults:
  # Calls the list controller, defined in the annotation of the contact entity.
    _entity_list: 'content_entity_example_contact'
    _title: 'Contact List'
  requirements:
  # Checks for permission directly.
    _permission: 'view contact entity'

content_entity_example.contact_add:
  path: '/content_entity_example_contact/add'
  defaults:
  # Calls the form.add controller, defined in the contact entity.
    _entity_form: content_entity_example_contact.add
    _title: 'Add Contact'
  requirements:
    _entity_create_access: 'content_entity_example_contact'

entity.content_entity_example_contact.edit_form:
  path: '/content_entity_example_contact/{content_entity_example_contact}/edit'
  defaults:
  # Calls the form.edit controller, defined in the contact entity.
    _entity_form: content_entity_example_contact.edit
    _title: 'Edit Contact'
  requirements:
    _entity_access: 'content_entity_example_contact.edit'

entity.content_entity_example_contact.delete_form:
  path: '/contact/{content_entity_example_contact}/delete'
  defaults:
    # Calls the form.delete controller, defined in the contact entity.
    _entity_form: content_entity_example_contact.delete
    _title: 'Delete Contact'
  requirements:
    _entity_access: 'content_entity_example_contact.delete'

content_entity_example.contact_settings:
  path: 'admin/structure/content_entity_example_contact_settings'
  defaults:
    _form: '\Drupal\content_entity_example\Form\ContactSettingsForm'
    _title: 'Contact Settings'
  requirements:
    _permission: 'administer contact entity'
```   
    
#### content_entity_example.links.menu.yml
设置管理员菜单。

```php
# Define the menu links for this module

entity.content_entity_example_contact.collection:
  title: 'Content Entity Example: Contacts Listing'
  route_name: entity.content_entity_example_contact.collection
  description: 'List Contacts'
  weight: 10
content_entity_example_contact.admin.structure.settings:
  title: Contact Settings
  description: 'Configure Contact entity'
  route_name:  content_entity_example.contact_settings
  parent: system.admin_structure
```
  
#### content_entity_example.links.action.yml
设置操作菜单。

```php
# All action links for this module

content_entity_example.contact_add:
  # Which route will be called by the link
  route_name: content_entity_example.contact_add
  title: 'Add Contact'

  # Where will the link appear, defined by route name.
  appears_on:
    - entity.content_entity_example_contact.collection
    - entity.content_entity_example_contact.canonical
```

#### content_entity_example.links.task.yml
设置tab切换菜单。

```php
# Define the 'local' links for the module

contact.settings_tab:
  route_name: content_entity_example.contact_settings
  title: Settings
  base_route: content_entity_example.contact_settings

contact.view:
  route_name: entity.content_entity_example_contact.canonical
  base_route: entity.content_entity_example_contact.canonical
  title: View

contact.page_edit:
  route_name: entity.content_entity_example_contact.edit_form
  base_route: entity.content_entity_example_contact.canonical
  title: Edit

contact.delete_confirm:
  route_name:  entity.content_entity_example_contact.delete_form
  base_route:  entity.content_entity_example_contact.canonical
  title: Delete
  weight: 10
```

###3、实体类型类(Entity type class)

#### src/ContactInterface.php

这个文件定义了一个ContaceInterface类，并调用EntityOwnerInterface获取其他的一些功能。
```php
<?php

namespace Drupal\content_entity_example;

use Drupal\Core\Entity\ContentEntityInterface;
use Drupal\user\EntityOwnerInterface;
use Drupal\Core\Entity\EntityChangedInterface;

/**
 * Provides an interface defining a Contact entity.
 * @ingroup content_entity_example
 */
interface ContactInterface extends ContentEntityInterface, EntityOwnerInterface, EntityChangedInterface {

}

?>
```

#### src/Entity/Contact.php
这个文件定义了contact实体。
```php
<?php

namespace Drupal\content_entity_example\Entity;

use Drupal\Core\Entity\EntityStorageInterface;
use Drupal\Core\Field\BaseFieldDefinition;
use Drupal\Core\Entity\ContentEntityBase;
use Drupal\Core\Entity\EntityTypeInterface;
use Drupal\content_entity_example\ContactInterface;
use Drupal\user\UserInterface;

/**
 * Defines the Contact entity.
 *
 * @ingroup content_entity_example
 *
 * This is the main definition of the entity type. From it, an entityType is
 * derived. The most important properties in this example are listed below.
 *
 * id: The unique identifier of this entityType. It follows the pattern
 * 'moduleName_xyz' to avoid naming conflicts.
 *
 * label: Human readable name of the entity type.
 *
 * handlers: Handler classes are used for different tasks. You can use
 * standard handlers provided by D8 or build your own, most probably derived
 * from the standard class. In detail:
 *
 * - view_builder: we use the standard controller to view an instance. It is
 *   called when a route lists an '_entity_view' default for the entityType
 *   (see routing.yml for details. The view can be manipulated by using the
 *   standard drupal tools in the settings.
 *
 * - list_builder: We derive our own list builder class from the
 *   entityListBuilder to control the presentation.
 *   If there is a view available for this entity from the views module, it
 *   overrides the list builder. @todo: any view? naming convention?
 *
 * - form: We derive our own forms to add functionality like additional fields,
 *   redirects etc. These forms are called when the routing list an
 *   '_entity_form' default for the entityType. Depending on the suffix
 *   (.add/.edit/.delete) in the route, the correct form is called.
 *
 * - access: Our own accessController where we determine access rights based on
 *   permissions.
 *
 * More properties:
 *
 *  - base_table: Define the name of the table used to store the data. Make sure
 *    it is unique. The schema is automatically determined from the
 *    BaseFieldDefinitions below. The table is automatically created during
 *    installation.
 *
 *  - fieldable: Can additional fields be added to the entity via the GUI?
 *    Analog to content types.
 *
 *  - entity_keys: How to access the fields. Analog to 'nid' or 'uid'.
 *
 *  - links: Provide links to do standard tasks. The 'edit-form' and
 *    'delete-form' links are added to the list built by the
 *    entityListController. They will show up as action buttons in an additional
 *    column.
 *
 * There are many more properties to be used in an entity type definition. For
 * a complete overview, please refer to the '\Drupal\Core\Entity\EntityType'
 * class definition.
 *
 * The following construct is the actual definition of the entity type which
 * is read and cached. Don't forget to clear cache after changes.
 *
 * @ContentEntityType(
 *   id = "content_entity_example_contact",
 *   label = @Translation("Contact entity"),
 *   handlers = {
 *     "view_builder" = "Drupal\Core\Entity\EntityViewBuilder",
 *     "list_builder" = "Drupal\content_entity_example\Entity\Controller\ContactListBuilder",
 *     "views_data" = "Drupal\views\EntityViewsData",
 *     "form" = {
 *       "add" = "Drupal\content_entity_example\Form\ContactForm",
 *       "edit" = "Drupal\content_entity_example\Form\ContactForm",
 *       "delete" = "Drupal\content_entity_example\Form\ContactDeleteForm",
 *     },
 *     "access" = "Drupal\content_entity_example\ContactAccessControlHandler",
 *   },
 *   base_table = "contact",
 *   admin_permission = "administer content_entity_example entity",
 *   fieldable = TRUE,
 *   entity_keys = {
 *     "id" = "id",
 *     "label" = "name",
 *     "uuid" = "uuid"
 *   },
 *   links = {
 *     "canonical" = "/content_entity_example_contact/{content_entity_example_contact}",
 *     "edit-form" = "/content_entity_example_contact/{content_entity_example_contact}/edit",
 *     "delete-form" = "/contact/{content_entity_example_contact}/delete",
 *     "collection" = "/content_entity_example_contact/list"
 *   },
 *   field_ui_base_route = "content_entity_example.contact_settings",
 * )
 *
 * The 'links' above are defined by their path. For core to find the corresponding
 * route, the route name must follow the correct pattern:
 *
 * entity.<entity-name>.<link-name> (replace dashes with underscores)
 * Example: 'entity.content_entity_example_contact.canonical'
 *
 * See routing file above for the corresponding implementation
 *
 * The 'Contact' class defines methods and fields for the contact entity.
 *
 * Being derived from the ContentEntityBase class, we can override the methods
 * we want. In our case we want to provide access to the standard fields about
 * creation and changed time stamps.
 *
 * Our interface (see ContactInterface) also exposes the EntityOwnerInterface.
 * This allows us to provide methods for setting and providing ownership
 * information.
 *
 * The most important part is the definitions of the field properties for this
 * entity type. These are of the same type as fields added through the GUI, but
 * they can by changed in code. In the definition we can define if the user with
 * the rights privileges can influence the presentation (view, edit) of each
 * field.
 */
class Contact extends ContentEntityBase implements ContactInterface {

  /**
   * {@inheritdoc}
   *
   * When a new entity instance is added, set the user_id entity reference to
   * the current user as the creator of the instance.
   */
  public static function preCreate(EntityStorageInterface $storage_controller, array &$values) {
    parent::preCreate($storage_controller, $values);
    $values += array(
      'user_id' => \Drupal::currentUser()->id(),
    );
  }

  /**
   * {@inheritdoc}
   */
  public function getCreatedTime() {
    return $this->get('created')->value;
  }

  /**
   * {@inheritdoc}
   */
  public function getChangedTime() {
    return $this->get('changed')->value;
  }

  /**
   * {@inheritdoc}
   */
  public function setChangedTime($timestamp) {
    $this->set('changed', $timestamp);
    return $this;
  }

  /**
   * {@inheritdoc}
   */
  public function getChangedTimeAcrossTranslations()  {
    $changed = $this->getUntranslated()->getChangedTime();
    foreach ($this->getTranslationLanguages(FALSE) as $language)    {
      $translation_changed = $this->getTranslation($language->getId())->getChangedTime();
      $changed = max($translation_changed, $changed);
    }
    return $changed;
  }

  /**
   * {@inheritdoc}
   */
  public function getOwner() {
    return $this->get('user_id')->entity;
  }

  /**
   * {@inheritdoc}
   */
  public function getOwnerId() {
    return $this->get('user_id')->target_id;
  }

  /**
   * {@inheritdoc}
   */
  public function setOwnerId($uid) {
    $this->set('user_id', $uid);
    return $this;
  }

  /**
   * {@inheritdoc}
   */
  public function setOwner(UserInterface $account) {
    $this->set('user_id', $account->id());
    return $this;
  }

  /**
   * {@inheritdoc}
   *
   * Define the field properties here.
   *
   * Field name, type and size determine the table structure.
   *
   * In addition, we can define how the field and its content can be manipulated
   * in the GUI. The behaviour of the widgets used can be determined here.
   */
  public static function baseFieldDefinitions(EntityTypeInterface $entity_type) {

    // Standard field, used as unique if primary index.
    $fields['id'] = BaseFieldDefinition::create('integer')
      ->setLabel(t('ID'))
      ->setDescription(t('The ID of the Contact entity.'))
      ->setReadOnly(TRUE);

    // Standard field, unique outside of the scope of the current project.
    $fields['uuid'] = BaseFieldDefinition::create('uuid')
      ->setLabel(t('UUID'))
      ->setDescription(t('The UUID of the Contact entity.'))
      ->setReadOnly(TRUE);

    // Name field for the contact.
    // We set display options for the view as well as the form.
    // Users with correct privileges can change the view and edit configuration.

    $fields['name'] = BaseFieldDefinition::create('string')
      ->setLabel(t('Name'))
      ->setDescription(t('The name of the Contact entity.'))
      ->setSettings(array(
        'default_value' => '',
        'max_length' => 255,
        'text_processing' => 0,
      ))
      ->setDisplayOptions('view', array(
        'label' => 'above',
        'type' => 'string',
        'weight' => -6,
      ))
      ->setDisplayOptions('form', array(
        'type' => 'string',
        'weight' => -6,
      ))
      ->setDisplayConfigurable('form', TRUE)
      ->setDisplayConfigurable('view', TRUE);

    $fields['first_name'] = BaseFieldDefinition::create('string')
      ->setLabel(t('First Name'))
      ->setDescription(t('The first name of the Contact entity.'))
      ->setSettings(array(
        'default_value' => '',
        'max_length' => 255,
        'text_processing' => 0,
      ))
      ->setDisplayOptions('view', array(
        'label' => 'above',
        'type' => 'string',
        'weight' => -5,
      ))
      ->setDisplayOptions('form', array(
        'type' => 'string',
        'weight' => -5,
      ))
      ->setDisplayConfigurable('form', TRUE)
      ->setDisplayConfigurable('view', TRUE);

    // Gender field for the contact.
    // ListTextType with a drop down menu widget.
    // The values shown in the menu are 'male' and 'female'.
    // In the view the field content is shown as string.
    // In the form the choices are presented as options list.
    $fields['gender'] = BaseFieldDefinition::create('list_string')
      ->setLabel(t('Gender'))
      ->setDescription(t('The gender of the Contact entity.'))
      ->setSettings(array(
        'allowed_values' => array(
          'female' => 'female',
          'male' => 'male',
        ),
      ))
      ->setDisplayOptions('view', array(
        'label' => 'above',
        'type' => 'string',
        'weight' => -4,
      ))
      ->setDisplayOptions('form', array(
        'type' => 'options_select',
        'weight' => -4,
      ))
      ->setDisplayConfigurable('form', TRUE)
      ->setDisplayConfigurable('view', TRUE);

    // Owner field of the contact.
    // Entity reference field, holds the reference to the user object.
    // The view shows the user name field of the user.
    // The form presents a auto complete field for the user name.
    $fields['user_id'] = BaseFieldDefinition::create('entity_reference')
      ->setLabel(t('User Name'))
      ->setDescription(t('The Name of the associated user.'))
      ->setSetting('target_type', 'user')
      ->setSetting('handler', 'default')
      ->setDisplayOptions('view', array(
        'label' => 'above',
        'type' => 'entity_reference',
        'weight' => -3,
      ))
      ->setDisplayOptions('form', array(
        'type' => 'entity_reference_autocomplete',
        'settings' => array(
          'match_operator' => 'CONTAINS',
          'size' => 60,
          'autocomplete_type' => 'tags',
          'placeholder' => '',
        ),
        'weight' => -3,
      ))
      ->setDisplayConfigurable('form', TRUE)
      ->setDisplayConfigurable('view', TRUE);

    $fields['langcode'] = BaseFieldDefinition::create('language')
      ->setLabel(t('Language code'))
      ->setDescription(t('The language code of Contact entity.'));
    $fields['created'] = BaseFieldDefinition::create('created')
      ->setLabel(t('Created'))
      ->setDescription(t('The time that the entity was created.'));

    $fields['changed'] = BaseFieldDefinition::create('changed')
      ->setLabel(t('Changed'))
      ->setDescription(t('The time that the entity was last edited.'));

    return $fields;
  }
}

?>
```
###4、Controller
#### src/Form/ContactForm.php
添加和修改contact内容实体的表单。
```php
<?php

namespace Drupal\content_entity_example\Form;

use Drupal\Core\Entity\ContentEntityForm;
use Drupal\Core\Language\Language;
use Drupal\Core\Form\FormStateInterface;

/**
 * Form controller for the content_entity_example entity edit forms.
 *
 * @ingroup content_entity_example
 */
class ContactForm extends ContentEntityForm {

  /**
   * {@inheritdoc}
   */
  public function buildForm(array $form, FormStateInterface $form_state) {
    /* @var $entity \Drupal\content_entity_example\Entity\Contact */
    $form = parent::buildForm($form, $form_state);
    $entity = $this->entity;

    $form['langcode'] = array(
      '#title' => $this->t('Language'),
      '#type' => 'language_select',
      '#default_value' => $entity->getUntranslated()->language()->getId(),
      '#languages' => Language::STATE_ALL,
    );
    return $form;
  }

  /**
   * {@inheritdoc}
   */
  public function save(array $form, FormStateInterface $form_state) {
    $form_state->setRedirect('entity.content_entity_example_contact.collection');
    $entity = $this->getEntity();
    $entity->save();
  }
}

?>
```

#### src/Form/ContactDeleteForm.php
删除确认表单。
```php
<?php

namespace Drupal\content_entity_example\Form;

use Drupal\Core\Entity\ContentEntityConfirmFormBase;
use Drupal\Core\Form\FormStateInterface;
use Drupal\Core\Url;

/**
 * Provides a form for deleting a content_entity_example entity.
 *
 * @ingroup content_entity_example
 */
class ContactDeleteForm extends ContentEntityConfirmFormBase {

  /**
   * {@inheritdoc}
   */
  public function getQuestion() {
    return $this->t('Are you sure you want to delete entity %name?', array('%name' => $this->entity->label()));
  }

  /**
   * {@inheritdoc}
   *
   * If the delete command is canceled, return to the contact list.
   */
  public function getCancelURL() {
    return new Url('entity.content_entity_example_contact.collection');
  }

  /**
   * {@inheritdoc}
   */
  public function getConfirmText() {
    return $this->t('Delete');
  }

  /**
   * {@inheritdoc}
   *
   * Delete the entity and log the event. log() replaces the watchdog.
   */
  public function submitForm(array &$form, FormStateInterface $form_state) {
    $entity = $this->getEntity();
    $entity->delete();

    \Drupal::logger('content_entity_example')->notice('@type: deleted %title.',
      array(
        '@type' => $this->entity->bundle(),
        '%title' => $this->entity->label(),
      ));
    $form_state->setRedirect('entity.content_entity_example_contact.collection');
  }

}
?>
```

#### src/Entity/Controller/ContactListBuilder.php
列表表单。
```php
<?php

namespace Drupal\content_entity_example\Entity\Controller;

use Drupal\Core\Entity\EntityInterface;
use Drupal\Core\Entity\EntityListBuilder;
use Drupal\Core\Url;

/**
 * Provides a list controller for content_entity_example_contact entity.
 *
 * @ingroup content_entity_example
 */
class ContactListBuilder extends EntityListBuilder {

  /**
   * {@inheritdoc}
   *
   * We override ::render() so that we can add our own content above the table.
   * parent::render() is where EntityListBuilder creates the table using our
   * buildHeader() and buildRow() implementations.
   */
  public function render() {
    $build['description'] = array(
      '#markup' => $this->t('Content Entity Example implements a Contacts model. These contacts are fieldable entities. You can manage the fields on the <a href="@adminlink">Contacts admin page</a>.', array(
        '@adminlink' => \Drupal::urlGenerator()->generateFromRoute('content_entity_example.contact_settings'),
      )),
    );
    $build['table'] = parent::render();
    return $build;
  }

  /**
   * {@inheritdoc}
   *
   * Building the header and content lines for the contact list.
   *
   * Calling the parent::buildHeader() adds a column for the possible actions
   * and inserts the 'edit' and 'delete' links as defined for the entity type.
   */
  public function buildHeader() {
    $header['id'] = $this->t('ContactID');
    $header['name'] = $this->t('Name');
    $header['first_name'] = $this->t('First Name');
    $header['gender'] = $this->t('Gender');
    return $header + parent::buildHeader();
  }

  /**
   * {@inheritdoc}
   */
  public function buildRow(EntityInterface $entity) {
    /* @var $entity \Drupal\content_entity_example\Entity\Contact */
    $row['id'] = $entity->id();
    $row['name'] = $entity->link();
    $row['first_name'] = $entity->first_name->value;
    $row['gender'] = $entity->gender->value;
    return $row + parent::buildRow($entity);
  }

}
?>
```