Drupal 8提供了一个Form API用于创建和管理表单，使用它你无需写任何HTML代码。

Drupal 8可以很方便的处理表单的创建、验证、提交等POST请求。

开发人员只需简单地在一个表单中定义元素，提供表单的验证方式，然后通过表单方法处理成功提交的数据。

Drupal 8中，表单和表单状态都是对象。

文档见：https://www.drupal.org/docs/8/api/form-api/introduction-to-form-api

###1、表单元素

* type: 表单类型，如textfield, textarea, html5又包含tel,email,number,date,url,search,range等

* title: 表单标题

* value: 表单值

* default_value: 表单默认值

* weight: 权重，可控制表单元素的位置

* placeholder: 占位符，如输入密码的文本框中显示"请输入密码"


###2、表单状态

\Drupal\Core\Form\FormStateInterface对象呈现了表单和数据的当前状态。表单状态包含用户提交的数据和创建状态信息。表单提交后的重定向、表单验证和表单提交都是通过form state处理的。

###3、表单缓存
Drupal为表单使用一个缓存表，它存储了通过表单创建的标识符。这样Drupal在使用AJAX验证表单时可以防止数组丢失。