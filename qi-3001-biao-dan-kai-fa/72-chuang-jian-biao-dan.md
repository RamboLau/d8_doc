###1、创建表单
首先来看表单的四个方法：

* getFormId: 必须定义的方法，返回表单机器名。

* buildForm: 必须定义的，它返回一个渲染数组，类似 hook_form。

* validateForm: 是可选的，进行校验，类似 hook_form_validate。

* submitForm: 执行提交处理，类似 hook_form_submit。

在src\Form目录下创建一个文件HelloWorldForm.php，代码如下：

```php
<?php

/** 
 * @file 
 * Contains \Drupal\hello_world\Form\HelloWorldForm. 
 */ 

namespace Drupal\hello_world\Form;

use Drupal\Core\Form\FormBase;
use Drupal\Core\Form\FormStateInterface;

class HelloWorldForm extends FormBase {

  /**
   * {@inheritdoc}.
   */
  public function getFormId() {
    return 'example_form';
  }

  /**
   * {@inheritdoc}.
   */
  public function buildForm(array $form, FormStateInterface $form_state) {
    $form['email'] = [
      '#type' => 'email',
      '#title' => $this->t('Your .com email address.')
    ];
    $form['show'] = [
      '#type' => 'submit',
      '#value' => $this->t('Submit'),
    ];
    return $form;
  }

  /**
   * {@inheritdoc}
   */
  public function validateForm(array &$form, FormStateInterface $form_state) { 
    if (strpos($form_state->getValue('email'), '.com') === FALSE) {
      $form_state->setErrorByName('email', $this->t('This is not a .com email address.'));
    }
  }

  /**
   * {@inheritdoc}
   */
  public function submitForm(array &$form, FormStateInterface $form_state) {
    drupal_set_message($this->t('Your email address is @email', ['@email' => $form_state->getValue('email')]));
  }
}
```

表单使用一个类来定义，实现了\Drupal\Core\Form\FormInterface接口。而 \Drupal\Core\Form\FormBase是表单的基类。form有textarea,textfield等类型。

###2、定义路由
路由追加到hello_world.routing.yml中，代码如下：
```php
hello_world.form:  
  path: '/hello/form'
  defaults:
    _form: '\Drupal\hello_world\Form\HelloWorldForm'
    _title: 'Demo Form'
  requirements:
    _permission: 'access simple page'
```
注意：这里把_controller改为了_form,表示调用一个form类。

drush cr清空缓存，然后访问/hello/form查看效果。

###3、如何在controller中调用form？

可以通过formBuilder方法来调用。

代码如下：
```php
$form = \Drupal::formBuilder()->getForm('Drupal\hello_world\Form\HelloWorldForm');
```