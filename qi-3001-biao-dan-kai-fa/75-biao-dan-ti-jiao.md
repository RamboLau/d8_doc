一个表单可以有多个提交处理器，通常所有表单都会实现一个提交处理器，即submitForm方法。表单将会自动执行::submitForm和任何定义在触发元素上的方法。也可以添加其他的一些方法。

如一个类提供了method1和method2，要想执行它们。可以在buildForm方法中添加下面的代码:

$form_state->setSubmitHandlers([
    ['::submitForm'],
    ['::method1'],
    [$this, 'method2']
]);

这一系列的提交处理器执行默认的submitForm方法和其它两个方法。你可以引用当前类定义的方法使用两个冒号和方法名。或者你可以使用由类实例和方法组成的数组。