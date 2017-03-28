上文中我们创建了一个实体类型，但是对数据没有做验证。

实体验证跟表单验证不同，而是使用实体验证API来完成。

###1、验证API
在任何类型化的数据对象上调用validate()方法实施验证，如:

```php
$definition = DataDefinition::create('integer')
   ->addConstraint('Range', ['min' => 5]);
$typed_data = \Drupal::typedDataManager()->create($definition, 10);
$violations = $typed_data->validate();
```

它返回一个验证列表，如果传递一个空的验证，则验证失败。

```php
if ($violations->count() > 0) {
   // Validation failed.
}
```

在这个例子中，在数据定义中指定了一个Range约束。然而这个类可能自动生成一些其它验证，或添加一些数据类型默认的验证约束。举一个例子，一个e-mail类型会添加字符串验证以及有效e-mail地址验证。可通过调用$type_data->getConstraints()返回所有已应用的验证约束。

在类型化数据对象上调用validate()获取一个Symfony验证器并验证数据是很快捷的，因为Symfony验证器对象已经配置为类型化数据。$type_data->validate()相当于:

```php
return \Drupal::typedDataManager()->getValidator()->validate($typed_data);
```

###2、实体验证

实体字段和字段项是类型化的数据对象，因此能够被验证，如:

```php
$violations = $entity->field_text->validate();
```

把实体作为一个整体来验证:
```php
$violations = $entity->validate();
```

$violations变量包含了验证失败的属性的路径，这个路径相对于验证开始的地方。例如，如果字段的文本值($field[0]->value)验证失败，在第一个例子中$violation->getPropertyPath()的属性路径为”0.value”，在第二个例子中它是”field_text.0.value”。

###3、给字段设置约束

通过setPropertyConstraints方法，可以很方便的给字段设置约束，如给字段设置一个最大长度的约束：

```
$fields['name'] = BaseFieldDefinition::create('string')
  ->setLabel(t('Name'))
  ->setPropertyConstraints('value', array('Length' => array('max' => 32)));
```



 