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

