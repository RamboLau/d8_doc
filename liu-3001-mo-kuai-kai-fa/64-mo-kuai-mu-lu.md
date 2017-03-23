###、创建目录
模块的机器名为”hello_world”，因此我们需要为模块创建hello_world目录，在drupal的安装目录/modules/custom/hello_world，或者/sites/all/modules/custom/hello_world。如果没有custom目录，请创建之。注意模块的目录名与机器名可以不同，比如我们使用HelloWorld作为模块的目录名，但请记住，模块的代码文件中必须使用模块机器名。**但是为了保证代码的可读性，我们规定模块目录名必须和模块机器名一样。**

以前的版本中自定义模块目录是放在/sites/all/modules下的，核心模块放在/modules。在Drupal 8 中，所有的核心模块、库等都放在/core目录中。贡献模块与自定义模块放在/modules目录中，但你也可以放在/sites/all/modules目录中，其结果是一样的。

我们这个模块现在还没有任何功能，我们需要先建立一个.info.yml文件，以使Drupal 8能发现它。

