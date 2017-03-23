 ###1、OOP
面向对象编程已经成为目前公认的最佳的编程方式。它将一切看作对象，对象具有属性和方法，属性表明对象状态，方法实现对象的操作。面向对象实现了软件的重用性、灵活性、扩展性目标。面向对象还包括诸如抽象、继承、多态等特性。

Drupal 8支持面向对象特性，其核心大多使用类、接口写成。并遵循一些公共的设计标准，如
* The Factory Pattern(http://design-patterns.readthedocs.io/zh_CN/latest/creational_patterns/simple_factory.html)

* Software design pattern (https://design-patterns.readthedocs.io/zh_CN/latest/)

###2、Symfony 2
Drupal 8引入了Symfony 2开发框架，目的是为了减少代码重复。Drupal 8主要用它处理路由(routing)、session和服务(service)。阅读Symfony 2手册(https://symfony-docs-chs.readthedocs.io/en/latest/) 以学习更多的Symfony框架知识。学习它根本不需要任何Drupal 知识，学习它将使你成为更好的Drupal开发者和PHP开发者。

###3、命名空间
如果你对PHP命名空间概念不熟悉，请阅读 http://php.net/manual/zh/language.namespaces.basics.php 。Drupal 8使用了PSR-4标准开发。PSR-4标准是基于PHP命名空间的自动加载包的方式。它定了如何自动加载基于命名空间和类名的类。Drupal 代码的命名空间是按照模块命名的如 block.module命名空间为Drupal\block。

PSR-4规定文件仅仅包含一个类、接口或trait。这些文件的名称必须与所包含的类、接口或trait的名称相同。以便于类加载器进行自动加载。

###4、依赖注入

###5、注解

###6、插件

###7、service

###8、YAML