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
Database::getConnection()->schema()->dropField($table, $field);
```

### 索引更改

#### 添加索引
```php
Database::getConnection()->schema()->addIndex($table, $name, $fields, $spec);
```

#### 删除索引
```php
Database::getConnection()->schema()->dropIndex($table, $name);
```

#### 添加唯一索引
```php
Database::getConnection()->schema()->addUniqueKey($table, $name, $fields);
```

#### 删除唯一索引
```php
Database::getConnection()->schema()->dropUniqueKey($table, $name);
```

#### 添加主键
```php
Database::getConnection()->schema()->addPrimaryKey($table, $fields);
```

#### 删除主键
```php
Database::getConnection()->schema()->dropPrimaryKey($table);
```

###表操作

#### 创建表
```php
Database::getConnection()->schema()->createTable($name, $table);
```

#### 删除表
```php
Database::getConnection()->schema()->dropTable($table);
```

#### 重命名表名
```php
Database::getConnection()->schema()->renameTable($table, $new_name);
```

####å 清空表
```php
Database::getConnection($options['target'])->truncate($table, $options);
```

完成以后，执行drush updb完成更新！