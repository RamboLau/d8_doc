 ###1、OOP
面向对象编程已经成为目前公认的最佳的编程方式。它将一切看作对象，对象具有属性和方法，属性表明对象状态，方法实现对象的操作。面向对象实现了软件的重用性、灵活性、扩展性目标。面向对象还包括诸如抽象、继承、多态等特性。

Drupal 8支持面向对象特性，其核心大多使用类、接口写成。并遵循一些公共的设计标准，如
* The Factory Pattern(http://design-patterns.readthedocs.io/zh_CN/latest/creational_patterns/simple_factory.html)

* Software design pattern (https://design-patterns.readthedocs.io/zh_CN/latest/)

###2、Symfony 2
Drupal 8引入了Symfony 2开发框架，目的是为了减少代码重复。Drupal 8主要用它处理路由(routing)、session和服务(service)。阅读Symfony 2手册(https://symfony-docs-chs.readthedocs.io/en/latest/) 以学习更多的Symfony框架知识。学习它根本不需要任何Drupal 知识，学习它将使你成为更好的Drupal开发者和PHP开发者。

###3、命名空间
如果你对PHP命名空间概念不熟悉，请阅读 http://php.net/manual/zh/language.namespaces.basics.php 。Drupal 8使用了PSR-4标准开发。PSR-4标准是基于PHP命名空间的自动加载包的方式。它定了如何自动加载基于命名空间和类名的类。Drupal 代码的命名空间是按照模块命名的如 block.module命名空间为Drupal\block。

PSR-4规定文件仅仅包含一个类、接口或trait。这些文件的名称必须与所包含的类、接口或trait的名称相同以便于类加载器进行自动加载。

###4、依赖注入
依赖注入已经成为了一种OOP设计模式，因为Drupal 8需要大量地调用核心API或核心服务，所以应该把它作为一种概念来理解。请阅读 https://laravel-china.github.io/php-the-right-way/#dependency_injection ，以便更好地理解这种方式。Drupal 8 的服务调用请阅读 https://www.drupal.org/docs/8/api/services-and-dependency-injection/services-and-dependency-injection-in-drupal-8 。

###5、注解
Drupal 8采用了注解方式，通过特定的插件可以找到并为代码提供一些附加内容。注释能被Doctrine注释解析器解析，并能转化为Drupal能使用的信息，以便于更好地理解你代码的功能。请阅读 http://verynull.com/2015/12/12/Drupal8%E6%B3%A8%E8%A7%A3-Annotations-%E8%AF%AD%E6%B3%95/

###6、插件
插件提供一些小功能，使用插件更易于在各种功能之间切换。具有相似功能的插件组成一个插件类型。例如’字段部件’是一种插件类型，它包括诸如文本框、文本区域、日期等不同的插件。

###7、service

###8、YAML