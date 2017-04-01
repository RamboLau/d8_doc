通常，使用hook_update_N来完成，N代表次数,Drupal8 N从8001开始。

在hook_update_N中可以很方便的添加、修改、删除字段和索引，更改表名，删除表等。

### 字段更改

#### 添加字段
```php
Database::getConnection()->schema()->addField($table, $field, $spec, $keys_new);
```

#### 修改字段
```php
Database::getConnection()->schema()->changeField($table, $field, $field_new, $spec, $keys_new);
```

#### 删除字段
```php

```

### 索引更改

#### 添加索引

#### 修改索引

#### 删除索引

###表操作


完成以后，执行drush updb完成更新！