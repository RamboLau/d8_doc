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


