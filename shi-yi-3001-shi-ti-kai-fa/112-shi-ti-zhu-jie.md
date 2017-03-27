配置实体类型和内容实体类型都是通过注解来定义的。

来看核心的一段代码，在core/modules/user/src/Entity/User.php

```php
<?php

namespace Drupal\user\Entity;

use Drupal\Core\Entity\ContentEntityBase;
use Drupal\Core\Entity\EntityChangedTrait;
use Drupal\Core\Entity\EntityStorageInterface;
use Drupal\Core\Entity\EntityTypeInterface;
use Drupal\Core\Field\BaseFieldDefinition;
use Drupal\Core\Language\LanguageInterface;
use Drupal\user\RoleInterface;
use Drupal\user\UserInterface;

/**
 * Defines the user entity class.
 *
 * The base table name here is plural, despite Drupal table naming standards,
 * because "user" is a reserved word in many databases.
 *
 * @ContentEntityType(
 *   id = "user",
 *   label = @Translation("User"),
 *   handlers = {
 *     "storage" = "Drupal\user\UserStorage",
 *     "storage_schema" = "Drupal\user\UserStorageSchema",
 *     "access" = "Drupal\user\UserAccessControlHandler",
 *     "list_builder" = "Drupal\user\UserListBuilder",
 *     "views_data" = "Drupal\user\UserViewsData",
 *     "route_provider" = {
 *       "html" = "Drupal\user\Entity\UserRouteProvider",
 *     },
 *     "form" = {
 *       "default" = "Drupal\user\ProfileForm",
 *       "cancel" = "Drupal\user\Form\UserCancelForm",
 *       "register" = "Drupal\user\RegisterForm"
 *     },
 *     "translation" = "Drupal\user\ProfileTranslationHandler"
 *   },
 *   admin_permission = "administer users",
 *   base_table = "users",
 *   data_table = "users_field_data",
 *   label_callback = "user_format_name",
 *   translatable = TRUE,
 *   entity_keys = {
 *     "id" = "uid",
 *     "langcode" = "langcode",
 *     "uuid" = "uuid"
 *   },
 *   links = {
 *     "canonical" = "/user/{user}",
 *     "edit-form" = "/user/{user}/edit",
 *     "cancel-form" = "/user/{user}/cancel",
 *     "collection" = "/admin/people",
 *   },
 *   field_ui_base_route = "entity.user.admin_form",
 *   common_reference_target = TRUE
 * )
 */
class User extends ContentEntityBase implements UserInterface {
...
}
```

注解使用了一下这些键：

* id: 实体类型唯一标识符。
* label: 实体标题用于UI，@Translation表明其可被翻译。
* handlers: 处理器，实现实体的各种功能。
* admin_permission: 实体的管理权限。
* base_table: 实体的基础表。
* data_table: 实体的数据表。
* translatable: 实体是否可被翻译。
* entity_keys: 实体键的数组。
* links: 使用URI模板语法的Link模板，它是一个数组。
* field_ui_base_route: 字段UI的基本路由。