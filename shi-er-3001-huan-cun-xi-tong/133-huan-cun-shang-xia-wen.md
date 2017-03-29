我们可以针对特定的上下文来制定缓存规则，比如在多站点中，有多个主题，可以配置每个主题的缓存规则不同。

缓存上下文是一个字符串，它关联到一个可用的缓存上下文服务(service)。缓存上下文以字符串集合进行传递，因此它们的类型提示为string[]。为什么类型为string[]呢？因为单个缓存项可能有很多依赖的缓存上下文。

###1、语法
* 父子分离
* 可指定参数，参数前加冒号

###2、核心缓存上下文

```bash
cookies
  :name
headers
  :name
ip
languages
  :type
request_format
route
  .book_navigation
  .menu_active_trails
    :menu_name
  .name
session
  .exists
theme
timezone
url
  .path
    .is_front // Available in 8.3.x or higher.
    .parent
  .query_args
    :key
    .pagers
      :pager_id
  .site
user
  .is_super_user
  .node_grants
    :operation
  .permissions
  .roles
    :role
```
读法如：user.permissions。

注意：如果出现了a.b.c和a.b，显然a.b包括了a.b.c，这样a.b.c就能省略。