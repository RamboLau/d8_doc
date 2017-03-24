首先来看表单的四个方法：

* getFormId: 必须定义的方法，返回表单机器名。
* buildForm: 必须定义的，它返回一个渲染数组，类似 hook_form 。
* validateForm: 是可选的，进行校验，类似 hook_form_validate  。
* submitForm: 执行提交处理，类似 hook_form_submit 。