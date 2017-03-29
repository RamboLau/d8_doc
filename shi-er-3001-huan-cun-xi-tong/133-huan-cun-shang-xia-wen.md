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

###3、自定义缓存上下文

任何模块都可以实现缓存上下文，缓存上下文是被标记为cache_context的服务。可以搜索一下核心都有哪些模块实现了缓存上下文。

```bash
jerry@mac:~/Sites/drupal8 > grep "cache_context" * -R | grep '.yml'
core/core.services.yml:    required_cache_contexts: ['languages:language_interface', 'theme', 'user.permissions']
core/core.services.yml:  cache_context.ip:
core/core.services.yml:  cache_context.headers:
core/core.services.yml:  cache_context.cookies:
core/core.services.yml:  cache_context.session:
core/core.services.yml:  cache_context.session.exists:
core/core.services.yml:  cache_context.request_format:
core/core.services.yml:  cache_context.url:
core/core.services.yml:  cache_context.url.site:
core/core.services.yml:  cache_context.url.path:
core/core.services.yml:  cache_context.url.path.parent:
core/core.services.yml:  cache_context.url.query_args:
core/core.services.yml:  cache_context.url.query_args.pagers:
core/core.services.yml:  cache_context.route:
core/core.services.yml:  cache_context.route.name:
core/core.services.yml:  cache_context.route.menu_active_trails:
core/core.services.yml:  cache_context.user:
core/core.services.yml:  cache_context.user.permissions:
core/core.services.yml:  cache_context.user.roles:
core/core.services.yml:  cache_context.user.is_super_user:
core/core.services.yml:  cache_context.languages:
core/core.services.yml:  cache_context.theme:
core/core.services.yml:  cache_context.timezone:
core/core.services.yml:  cache_contexts_manager:
core/core.services.yml:    arguments: ['@service_container', '%cache_contexts%' ]
core/core.services.yml:    arguments: ['@language_manager', '@config.factory', '@page_cache_request_policy', '@page_cache_response_policy', '@cache_contexts_manager', '%http.response.debug_cacheability_headers%']
core/core.services.yml:    arguments: ['@request_stack', '@cache_factory', '@cache_contexts_manager', '@render_placeholder_generator']
core/modules/book/book.services.yml:  cache_context.route.book_navigation:
core/modules/node/node.services.yml:  cache_context.user.node_grants:
```

下面我们以book模块的缓存上下文来示例：

先创建一个service:
```php
  cache_context.route.book_navigation:
    class: Drupal\book\Cache\BookNavigationCacheContext
    arguments: ['@request_stack']
    calls:
      - [setContainer, ['@service_container']]
    tags:
      - { name: cache.context}
```