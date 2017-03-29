###1、嵌套锁
Drupal也支持事务(transactions)，并且数据库不支持事务时也能回退。但是，当你同时开始两个事务时，事务会变得相当复杂。这种情况随着数据库不同而变化。

同样的问题存在于C/C++的嵌套锁中。如果已经取得了A锁，还尝试请求A锁，代码将会死锁。你可以写代码来检测它是否取得了一个锁并且不能再取得这个锁，以避免发生死锁。

在SQL中，也存在相同的问题。如果你的代码已经在一个事务中，开启一个新的事务可能会造成意外或严重后果。

Java解决了嵌套锁。Java允许你将函数标记为'synchronized'，只要锁没有被释放，它就会一直等待。如果一个同步函数调用了同一个类中的其它函数，Java也会跟踪锁嵌套。外层的函数取得锁，内层函数不需要进行请求锁，外层函数返回时会去释放锁。

虽然我们不能在PHP中将函数申请为"transactional"，但我们可以通过使用对象的构造函数(constuct)和析构函数(destruct)来模拟Java的嵌套逻辑。调用"$transaction = db_transaction();"的函数首先将自身置于事务中。如果这个事务函数调用其它函数，我们的事务抽象层将它嵌套到函数中。这样，在内层函数中不进行锁请求操作，但外层的锁仍然可用。

###2、开启事务
要开启一个新的事务，只需调用$transaction = db_transaction()。这个事务会在$transaction的作用范围中开启。当$transaction销毁时，将会提交这个事务。如果你的事务嵌套在其它事务中，Drupal将会跟踪每个事务，并且仅仅提交最外层的事务。

你必须将db_transaction()的返回值赋给一个变量，如下面的例子。如果你直接调用这个函数没有将其赋给一个变量，你的事务将会立即提交。

代码如下:
```
function my_transaction_function() {
  // The transaction opens here.
  $transaction = db_transaction();

  try {
    $id = db_insert('example')
      ->fields(array(
        'field1' => 'mystring',
        'field2' => 5,
      ))
      ->execute();

    my_other_function($id);

    return $id;
  }
  catch (Exception $e) {
    $transaction->rollback();
    watchdog_exception('my_type', $e);
  }

  // $transaction goes out of scope here.  Unless it was rolled back, it
  // gets automatically commited here.
}

function my_other_function($id) {
  // The transaction is still open here.
  if ($id % 2 == 0) {
    db_update('example')
      ->condition('id', $id)
      ->fields(array('field2' => 10))
      ->execute();
  }
}
```

通常,数据库操作必须使用try...catch来捕获异常。