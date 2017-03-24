每个表单都可以有一个或多个验证方法来验证提交的表单数据。

以下是一个简单的表单验证方法，主要检测表单元素的值是否为空且长度大于5。

```php
/**
 * {@inheritdoc}
 */

public function validateForm(array &$form, FormStateInterface $form_state) {
    if (!$form_state->isValueEmpty('company_name')) {
        if (strlen($form_state->getValue('company_name')) <= 5) {
            $form_state->setErrorByName('company_name', t('Company name is less than 5 characters'));
        }
    }
}
```

* isValueEmpty: 用来验证值是否为空

* getValue: 获取元素的值

* setErrorByName: 抛出异常错误，第一个参数是元素名称，第二个参数是错误信息。

