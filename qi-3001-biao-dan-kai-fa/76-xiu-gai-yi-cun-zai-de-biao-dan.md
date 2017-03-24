修改已存在的表单有两个方法:

* hook_form_alter

* hook_form_FORM_ID_alter

这两个方法是通用的，通过这两个API可以很方便的修改核心模块或第三方模块的表单，让界面更加易用。

接下来我们来一段示例代码更改system_site_information_settings这个表单。

###1、hook_form_FORM_ID_alter
在hello_world.module中添加如下代码：

```php
/**
 * Implements hook_form_FORM_ID_alter().
 */

function hello_world_form_system_site_information_settings_alter(&$form, \Drupal\Core\Form\FormStateInterface $form_state) {
    $form['site_phone'] = array(
        '#type' => 'tel',
        '#title' => t('Site phone'),
        '#default_value' => Drupal::config('system.site')- >get('phone'),
    );
}
```

注意：$form是通过引用传递过来的。