缓存API文档见：https://www.drupal.org/docs/8/api/cache-api/cache-api

###1、缓存元数据
包含三个属性：
* cache tags: cache标签，如entities和configuration
* cache contexts: cache上下文，一般依赖于请求上下文
* cache max-age: cache生存周期

###2、缓存用法
前面的课程中讲到区块、实体、插件等，它们的控制器里不直接与缓存API交互，而是通过使用以下的一些方式来做缓存。

#### 渲染缓存(Render cache)
渲染API将缓存元数据嵌入到渲染数组以完成缓存。因此，缓存API不会与渲染缓存交互(既不获取缓存项也不创建新的缓存项)。
如：
```php
$renderer = \Drupal::service('renderer');
$config = \Drupal::config('system.site');
$build = [
  '#markup' => t('Hi, welcome back to @site!', [ 
    '@site' => $config->get('name'),
  ])
]; 
$renderer->addCacheableDependency($build, $config); 
```

#### 响应缓存(Response cache)
通常使用HtmlResponse来响应，它实现了CacheableResponseInterface接口。

Restful API使用ResourceResponse来响应请求。