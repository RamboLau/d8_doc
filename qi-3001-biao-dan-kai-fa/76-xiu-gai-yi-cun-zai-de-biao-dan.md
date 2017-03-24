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
    
    $form['#submit'][] = 'hello_world_system_site_information_phone_submit';
}
```

注意：$form是通过引用传递过来的。

定义submit方法，代码如下：

```php
/**
 * Form callback to save site_phone.
 *
 * @param array $form
 * @param \Drupal\Core\Form\FormStateInterface $form_state
 */

function hello_world_system_site_information_phone_submit(array &$form, \Drupal\Core\Form\FormStateInterface $form_state) {
    $config = Drupal::configFactory()->getEditable('system.site');
    $config
        ->set('phone', $form_state->getValue('site_phone'))
        ->save();

}
```

在后台验证保存是否成功。