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

