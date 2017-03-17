Drupal 8 在安装的最后一步，要求站点管理员设置国家和时区，这个设置就成为站点的默认国家和时区。一般设置为中国，北京时间。当然在安装完Drupal 8 后，可以对默认的国家的时区及日期格式进行修改。

### 设置国家和时区
点击管理->配置->区域设置或输入admin/config/regional/settings，在随后的页面中设置国家和时区，如下图所示。

drupal8-regional-country-settings

在LOCALE->Default country下拉列表框中，你会看到Drupal 8支持很多国家，默认国家可以设置为无，这里设置为中国(China)。接下来设置每周的第一天，按中国人的习惯设置为星期一。

时区设置，从TIME ZONES->Default time zone下拉列表框中选择一个时区，中国的话可以选择Asia/Shanghai或Asia/Chongqing，这两个地方分别分中国上海和中国重庆。在默认时区下方还可以进一步进行设置。一般来说不需要用户设置他们自己的时区，新用户的时区设为默认时区即可。设置完后点 保存配置。

 
### 日期时间格式设置
点击Manage->Configuration->Date and time formats，出现日期和时间格式管理界面，在这一页列出了drupal 8已创建的日期时间格式，这些格式与中国的时间日期格式有点不同，你可以对已存在的格式进行编辑和删除，也可以添加新的日期时间格式。

添加新的日期格式，在日期时间格式列表页面，点击添加格式按钮(Add format)或输入admin/config/regional/dete-time/formats/add(以后这种不完整的URL都是省略了域名)后，出现添加日期时间格式页面，如下图:

add_date_time_formats_in_drupal8

名称(Name):日期时间格式名称，如中国标准时间格式

格式字符串(Format string):PHP支持的日期时间格式字符串，如果不知道，可查阅PHP手册，这里设为Y-m-d H:i:s，设置后，文本框的右边会出现显示示例。设好后点添加格式(Add format)完成操作。

编辑日期格式所使用的表单和添加日期格式所使用的表单相似，只是表单已设置值，只需作修改即可。

 
