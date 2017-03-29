在Drupal中，schema是一个用来定义数据库结构的数组，在数组中定义字段名称、描述类型以及主键和索引等。
schema在modulename.install文件中的hook_schema()函数中定义。

hook_schema()函数的功能是定义模块需要的表结构。因为有这个API，你不需要关心不同数据库在创建或修改表时的语法。
hook_schema()返回一个定义表结构的关联数组。

常用的键:

    description:用于描述表用途的纯文本字符串。如果要引用其它表，请使用花括号。例如，node_field_revision表的描述包含”Stores per-revision title and body data for each {node}.”这里的{node}就是引用节点表。
    fields:描述数据库表的列的关联数组。形如(’fieldname’ => specification)，specification是一个数组，它可以使用下面的键:
        description:描述字段用途的纯文本字行串。引用其它的表应使用花括号。例如节点表的vid字段的描述包含”Always holds the largest(most recent){node_field_revision}.vid value for this nid”。其中的{node_field_revision}.vid。
        type:字段的数据类型:char,varchar,text,blob,int,float,numeric,serial。这些数据类型会映射成具体的数据库类型。如自动增加字段使用’serial’，在MySQL中它对于’INT auto_increment’。
        mysql_type,pgsql_type,sqlite_type,etc:如果你需要使用的记录类型没有包含在上面列出的官方支持列表中，你可以为每种数据库后端指定一种类型。在这种情况下，你可以省略类型参数，但建议你的schema在失败时加载一个没有那种类型的后端。一种可行的解决方案是使用”text”作为回退。
        serialize:指出字段值是否被保存为一个序列化的字符串。
        size:数据大小:tiny,small,medium,normal,big。能存储的最大值的提示，这取决于具体的数据库引擎，如MySQL上的TINYINT,INT,BIGINT等。默认为normal。
        not null:如果设为true，数据库列将不允许NULL值。默认为false。
        default:字段的默认值。这与PHP类型是有关的，’0’和0是不同的。如果你指定’0’作为一个int型字段的默认值这是不行的，因为它是一个字符串值。
        length:char,varchar,text字段类型的最大长度。忽略其它的字段类型。
        unsigned:指出是否为无符号数字型。默认为false。忽略其它类型。
        precision,scale:对于’numeric’字段，指出精度和小数位，这两个值是强制性的，对其它字段无效。
        binary:char,varchar或者text字段是否使用区分大小写的二进制collation。这对于那些已经把区分大小写作为默认行为的数据类型是没有影响的。
        除type外其它参数可选，但对于numeric列必须指定precision和scale，对于varchar必须指定length。
    primary key:指定主键的数组。
    unique keys:指定唯一键的关联数组。形如(‘keyname’ => specification)。每个specification是一个数组。
    foreign keys:定义外键的关联数组。
    indexes:定义索引的关联数组。

为了更好地理解schema的定义，下面是node表的一部份schema定义。它显示了四个字段(nid,vid,type,title)，主键为nid，唯一键为vid，两个外键，几个索引。

$schema['node'] = array(
  'description' => 'The base table for nodes.',
  'fields' => array(
    'nid'       => array('type' => 'serial', 'unsigned' => TRUE, 'not null' => TRUE),
    'vid'       => array('type' => 'int', 'unsigned' => TRUE, 'not null' => TRUE,'default' => 0),
    'type'      => array('type' => 'varchar','length' => 32,'not null' => TRUE, 'default' => ''),
    'language'  => array('type' => 'varchar','length' => 12,'not null' => TRUE,'default' => ''),
    'title'     => array('type' => 'varchar','length' => 255,'not null' => TRUE, 'default' => ''),
    'uid'       => array('type' => 'int', 'not null' => TRUE, 'default' => 0),
    'status'    => array('type' => 'int', 'not null' => TRUE, 'default' => 1),
    'created'   => array('type' => 'int', 'not null' => TRUE, 'default' => 0),
    'changed'   => array('type' => 'int', 'not null' => TRUE, 'default' => 0),
    'comment'   => array('type' => 'int', 'not null' => TRUE, 'default' => 0),
    'promote'   => array('type' => 'int', 'not null' => TRUE, 'default' => 0),
    'moderate'  => array('type' => 'int', 'not null' => TRUE,'default' => 0),
    'sticky'    => array('type' => 'int', 'not null' => TRUE, 'default' => 0),
    'translate' => array('type' => 'int', 'not null' => TRUE, 'default' => 0),
  ),
  'indexes' => array(
    'node_changed'        => array('changed'),
    'node_created'        => array('created'),
    'node_moderate'       => array('moderate'),
    'node_frontpage'      => array('promote', 'status', 'sticky', 'created'),
    'node_status_type'    => array('status', 'type', 'nid'),
    'node_title_type'     => array('title', array('type', 4)),
    'node_type'           => array(array('type', 4)),
    'uid'                 => array('uid'),
    'translate'           => array('translate'),
  ),
  'unique keys' => array(
    'vid' => array('vid'),
  ),
  // For documentation purposes only; foreign keys are not created in the
  // database.
  'foreign keys' => array(
    'node_revision' => array(
      'table' => 'node_field_revision',
      'columns' => array('vid' => 'vid'),
     ),
    'node_author' => array(
      'table' => 'users',
      'columns' => array('uid' => 'uid'),
     ),
   ),
  'primary key' => array('nid'),
);