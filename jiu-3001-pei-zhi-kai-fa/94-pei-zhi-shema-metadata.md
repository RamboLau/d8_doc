* schema 描述配置文件的结构
* metadata 配置的元数据

###1、一个简单的示例
有如下代码时:
```php
<?php
$config = \Drupal::config('system.maintenance');
$message  = $config->get('message');
$langcode = $config->get('langcode');
?>
```
config数据结构体的默认值是保存在配置文件core/modules/system/config/install/system.maintenance.yml 

代码如下:
```php
message: '@site is currently under maintenance. We should be back shortly. Thank you for your patience.'
langcode: en
```

在core/modules/system/config/schema/system.schema.yml文件中定义数据结构体。
```php
system.maintenance:
  type: config_object
  label: 'Maintenance mode'
  mapping:
    message:
      type: text
      label: 'Message to display when in maintenance mode'
```

schema定义了一个mapping数据结构体，可以认为是一个数组.
注意：一个schema.yml文件可以定义多个数据结构体，每一个数据结构体的机器名必须与setting.yml文件名一致。

###2、schema属性
* type: 有基本类型和派生类型

> 基本类型(core.data_types.schema.yml)：
```php
# Undefined type used by the system to assign to elements at any level where
# configuration schema is not defined. Using explicitly has the same effect as
# not defining schema, so there is no point in doing that.
undefined:
  label: 'Undefined'
  class: '\Drupal\Core\Config\Schema\Undefined'

# Explicit type to use when no data typing is possible. Instead of using this
# type, we strongly suggest you use configuration structures that can be
# described with other structural elements of schema, and describe your schema
# with those elements.
ignore:
  label: 'Ignore'
  class: '\Drupal\Core\Config\Schema\Ignore'

# Basic scalar data types from typed data.
boolean:
  label: 'Boolean'
  class: '\Drupal\Core\TypedData\Plugin\DataType\BooleanData'
email:
  label: 'Email'
  class: '\Drupal\Core\TypedData\Plugin\DataType\Email'
integer:
  label: 'Integer'
  class: '\Drupal\Core\TypedData\Plugin\DataType\IntegerData'
float:
  label: 'Float'
  class: '\Drupal\Core\TypedData\Plugin\DataType\FloatData'
string:
  label: 'String'
  class: '\Drupal\Core\TypedData\Plugin\DataType\StringData'
uri:
  label: 'Uri'
  class: '\Drupal\Core\TypedData\Plugin\DataType\Uri'
```

另外还有
config_object: 默认配置结构
config_entity: 实体配置结构

> 派生类型：
mapping: 关联数组(associative array)，key/value数据结构，只支持string类型
sequence: 索引数组(indexed array)

* label: 短标签
* translatable: 是否可被翻译
* nullable: 是否允许空值
* class: 实现的类名

###3、关于动态类型[%parent]
下面的示例我们定义了两个动态类型warning和multiple，multiple是一个列表，warning是一个当行文本。

config/install/hello_world.message.single.yml
```php
type: warning
message: 'Hello!'
langcode: en
```

config/install/hello_world.message.multiple.yml
```php
type: multiple
message:
 -'Hello!'
 -'Hi!' 
langcode: en
```

config/schema/hello_world.schema.yml
```php
hello_world.message.*:
 type: config_object 
 mapping:
  type:
   type: string
   label: 'Message type'
  message:
   type: hello_world_message.[%parent.type]
  langcode:	
   type: string
   label: 'Language code'

hello_world_message.warning:
 type: string

hello_world_message.multiple:
 type: sequence
 label: 'Messages'
 sequence:
  type: string
  label: 'Message'
```
* *表示通配符，这里指single或multiple
* %parent.type 指我们在下面定义的数据类型，注意：这里定义的机器名要和父节点的机器名不能重名

###4、关于动态类型 [type]
如果想要在已有的数据下面定义你自己不同的数据类型 那么[type]将会变的非常有用.
```php
config/install/my_module.message.single.yml
 message: 
  type: warning
  value: 'Hello!'
 langcode: en
 
config/install/my_module.message.multiple.yml
 message:
  type: multiple 
  value:
   -'Hello!'
   -'Hi!'
 langcode: en

config/schema/my_module.schema.yml
my_module.message.*:
 type: config_object
 mapping:
  message:
   type: my_module_message.[type]
  […]
  
my_module_message.warning:
 type: mapping
 […]
 
my_module_message.multiple:
 type: mapping
 […]
```

首先看my_module.schema.yml文件用[type]来动态定义message的类型，所以my_module.schema.yml这个文件中我们又看到了my_module_message.warning跟my_module_message.multiple这两个定义类型，
需要说明的是无论是my_module_message.warning还是my_module_message.multiple它们的类型都是mapping类型，那么它们对应的值也必须mapping类型，我们再来看my_module.message.single.yml跟
my_module.message.multiple.yml两个文件，message的值也是mapping类型。
你同样可以定义一个包含相同的key，例如’type’的 基础类型,并且继承这个基础的类型你可以扩展任何自定义类型的key。

5、关于动态类型[%key]

```php
config/install/my_module.messages.yml
messages: 
 'single:1': 'Hello!'
 'single:2': 'Hi!'
 'multiple:1':
  -'Good morning!'
  -'Good night!'
langcode: en
```

这是一个任意消息元素的列表.
```php
config/schema/my_module.schema.yml
 my_module.messages:
  type: config_object
  mapping:		
   messages:
    type: sequence
    label:'Messages'				
    sequence:				
     type: my_module_message.[%key] 
   langcode:
    type: string
    label: 'Language code'
 
 my_module_message.single:*:
  type: string
  label: 'Message'

 my_module_message.multiple:*:
  type: sequence
  label: 'Messages'
  sequence:
   type: string
   label: 'Message'	
```

首先看config/schema/my_module.schema.yml，messages的类型是sequence，而sequence的类型则是以my_module_message为前缀的动态类型，再往下看定义了my_module_message.single:*:跟 
my_module_message.multiple:*:两个结构类型，这两个类型对应的messages值分别是以'single:*'跟'multiple:*'为前缀的，所以我们在config/install/my_module.messages.yml这个文件中
看到了'single:*'跟'multiple:*'为前缀的的messages的默认值