如何更改我们在{module}.settings.yml定义的配置呢？通常我们通过form表单来实现。

如我们要实现这样的效果
![图片](https://www.drupal.org/files/Config.jpg)

### example/src/Form/exampleSettingsForm.php

```php
namespace Drupal\example\Form;

use Drupal\Core\Form\ConfigFormBase;
use Drupal\Core\Form\FormStateInterface;

/**
 * Configure example settings for this site.
 */
class exampleSettingsForm extends ConfigFormBase {
  /** 
   * {@inheritdoc}
   */
  public function getFormId() {
    return 'example_admin_settings';
  }

  /** 
   * {@inheritdoc}
   */
  protected function getEditableConfigNames() {
    return [
      'example.settings',
    ];
  }

  /** 
   * {@inheritdoc}
   */
  public function buildForm(array $form, FormStateInterface $form_state) {
    $config = $this->config('example.settings');

    $form['example_thing'] = array(
      '#type' => 'textfield',
      '#title' => $this->t('Things'),
      '#default_value' => $config->get('things'),
    );  

    $form['other_things'] = array(
      '#type' => 'textfield',
      '#title' => $this->t('Other things'),
      '#default_value' => $config->get('other_things'),
    );  

    return parent::buildForm($form, $form_state);
  }

  /** 
   * {@inheritdoc}
   */
  public function submitForm(array &$form, FormStateInterface $form_state) {
    // Retrieve the configuration
    $this->config('example.settings')
      // Set the submitted configuration setting
      ->set('things', $form_state->getValue('example_thing'))
      // You can set multiple configurations at once by making
      // multiple calls to set()
      ->set('other_things', $form_state->getValue('other_things'))
      ->save();

    parent::submitForm($form, $form_state);
  }
}
```

### example.routing.yml

```php
example.settings:
  path: '/admin/structure/example/settings'
  defaults:
    _form: '\Drupal\example\Form\exampleSettingsForm'
    _title: 'example'
  requirements:
    _permission: 'administer site configuration'
```

以上代码已经很清晰的说明了如何通过form表单来更改默认的配置。