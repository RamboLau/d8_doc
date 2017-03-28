服务: Service
依赖注入: Dependency Injection

Drupal8服务API文档：

https://www.drupal.org/docs/8/api/services-and-dependency-injection/services-and-dependency-injection-in-drupal-8


再来重温一下插件和服务的区别:

###1、插件

插件通过一个公共的接口实现不同的行为。例如，图像变换插件，一般有缩放、裁剪、去色等。每个变换类型以相同的方式接受一个图像，完成转换，返回转换后的图像。当然，每个变换的效果是不同的。

###2、服务

服务提供相同的功能，并且是可交换的，不同点仅仅在于它们的内部实现。比如缓存，缓存应该提供获取(get)、设置(set)、过期(expire)等方法。用户只需要一个缓存，从一种缓存换成另一种缓存没有任何功能上的差别。至于缓存中的方法的实现机制用户是不会管的。

比如从apc改成redis，仅仅是缓存的内部实现机制需要变换。

所以缓存应该设计成一种服务。
