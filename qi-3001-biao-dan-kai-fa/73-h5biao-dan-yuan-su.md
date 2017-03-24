Drupal8支持html5表单元素，所有的表单元素都是添加在buildForm方法中，以下是一些示例。

###1、电话号码(tel)

```php
$form['phone'] = array(
    '#type' => 'tel',
    '#title' => t('Phone'),
);
```

###2、邮箱(email)

```php
$form['email'] = array(
    '#type' => 'email',
    '#title' => t('Email'),
);
```

###3、数字(integer)

```php
$form['integer'] = array(
    '#type' => 'number',
    '#title' => t('Some integer'),
    // The increment or decrement amount
    '#step' => 1,
    // Miminum allowed value
    '#min' => 0,
    // Maxmimum allowed value
    '#max' => 100,
);
```

###4、日期(date)

```php
$form['date'] = array(
    '#type' => 'date',
    '#title' => t('Date'),
    '#date_date_format' => 'Y-m-d',
);
```

###5、输入URL(website)

```php
$form['website'] = array(
    '#type' => 'url',
    '#title' => t('Website'),
);
```

###6、搜索关键字(search)

```php
$form['search'] = array(
    '#type' => 'search',
    '#title' => t('Search'),
    '#autocomplete_route_name' => FALSE,
);
```
注意：填入正确的route_name，会启用自动完成搜索。

###7、范围(range)

```php
$form['range'] = array(
    '#type' => 'range',
    '#title' => t('Range'),
    '#min' => 0,
    '#max' => 100,
    '#step' => 1,
);
```