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