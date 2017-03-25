###1、覆写Global

Drupal8可以使用$config这个全局变量来访问配置。

通常通过Drupal\Core\Config\ConfigFactory::get()这个API来获取配置的值。

比如，我们获取网站维护的message:

```php
$message = \Drupal::config('system.maintenance')->get('message');
```

可以在setting.php中直接来覆写这个变量：
```php
$config['system.maintenance']['message'] = 'Sorry, our site is down now.';
```

在性能-> css合并这里，安装完以后Drupal8是默认打开的，关闭代码如下：

```php
$config['system.performance']['css']['preprocess'] = 0;
```

然后通过drush命令查看是否覆写成功。
```bash
drush config-list
drush config-get system.performance
```

###2、获取原值
配置被覆写了以后，怎么获取原值呢？以下是示例代码：

```php
// Get the site name, with overrides.
$site_name = \Drupal::config('system.site')->get('name');

// Get the site name without overrides.
$site_name = \Drupal::config('system.site')->getOriginal('name', FALSE);

// Note that mutable config is always override free.
$site_name = \Drupal::configFactory()->getEditable('system.site')->get('name');
```

###3、覆写语言配置
多语言站点中，要给用户发送邮件，这时候邮件语言取决于用户的语言配置。代码如下：

```php
// Load the language_manager service
$language_manager = \Drupal::service('language_manager');

// Get the target language object
$langcode = $account->getPreferredLangcode();
$language = $language_manager->getLanguage($langcode);

// Remember original language before this operation.
$original_language = $language_manager->getConfigOverrideLanguage();
// Set the translation target language on the configuration factory.
$language_manager->setConfigOverrideLanguage($language);

$mail_config  = \Drupal::config('user.mail');

// Now send email based on $mail_config which is in the proper language.

// Set the configuration language back.
$language_manager->setConfigOverrideLanguage($original_language);
```

这段代码主要是把user.mail这个配置更改为language.config.$langcode.user.mail，主要发送邮件的时候就会自动找用户设置的语言，从而找到跟用户语言相符合的邮件模板。

