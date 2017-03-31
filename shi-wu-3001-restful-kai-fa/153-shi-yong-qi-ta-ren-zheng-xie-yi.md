Drupal8核心提供了几个认证协议，有：

* Cookie
* HTTP Basic

第三方的有：

* OAuth
* Simple OAuth
* IP

### 自定义认证协议

目录结构：modules/custom/test/src/Authentication/Provider/DefaultAuthenticationProvider.php
可以使用
```php
drupal generate:authentication:provider
```
来创建一个认证。

#### 定义服务
```php
  authentication.test:
    class: Drupal\test\Authentication\Provider\DefaultAuthenticationProvider
    arguments: ['@config.factory', '@entity_type.manager']
    tags:
      - { name: authentication_provider, provider_id: default_authentication_provider, priority: 100 }
```

### 实现provider
```php
<?php

namespace Drupal\test\Authentication\Provider;

use Drupal\Core\Authentication\AuthenticationProviderInterface;
use Drupal\Core\Config\ConfigFactoryInterface;
use Drupal\Core\Entity\EntityTypeManagerInterface;
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpKernel\Event\GetResponseForExceptionEvent;
use Symfony\Component\HttpKernel\Exception\UnauthorizedHttpException;
use Symfony\Component\HttpKernel\Exception\AccessDeniedHttpException;

/**
 * Class DefaultAuthenticationProvider.
 *
 * @package Drupal\test\Authentication\Provider
 */
class DefaultAuthenticationProvider implements AuthenticationProviderInterface {

  /**
   * The config factory.
   *
   * @var \Drupal\Core\Config\ConfigFactoryInterface
   */
  protected $configFactory;

  /**
   * The entity type manager.
   *
   * @var \Drupal\Core\Entity\EntityTypeManagerInterface
   */
  protected $entityTypeManager;

  /**
   * Constructs a HTTP basic authentication provider object.
   *
   * @param \Drupal\Core\Config\ConfigFactoryInterface $config_factory
   *   The config factory.
   * @param \Drupal\Core\Entity\EntityTypeManagerInterface $entity_type_manager
   *   The entity type manager service.
   */
  public function __construct(ConfigFactoryInterface $config_factory, EntityTypeManagerInterface $entity_type_manager) {
    $this->configFactory = $config_factory;
    $this->entityTypeManager = $entity_type_manager;
  }

  /**
   * Checks whether suitable authentication credentials are on the request.
   *
   * @param \Symfony\Component\HttpFoundation\Request $request
   *   The request object.
   *
   * @return bool
   *   TRUE if authentication credentials suitable for this provider are on the
   *   request, FALSE otherwise.
   */
  public function applies(Request $request) {
    // If you return TRUE and the method Authentication logic fails,
    // you will get out from Drupal navigation if you are logged in.
    return FALSE;
  }

  /**
   * {@inheritdoc}
   */
  public function authenticate(Request $request) {
    $consumer_ip = $request->getClientIp();
    $ips = [];
    if (in_array($consumer_ip, $ips)) {
      // Return Anonymous user.
      return $this->entityTypeManager->getStorage('user')->load(0);
    }
    else {
      throw new AccessDeniedHttpException();
    }
  }

  /**
   * {@inheritdoc}
   */
  public function cleanup(Request $request) {}

  /**
   * {@inheritdoc}
   */
  public function handleException(GetResponseForExceptionEvent $event) {
    $exception = $event->getException();
    if ($exception instanceof AccessDeniedHttpException) {
      $event->setException(
        new UnauthorizedHttpException('Invalid consumer origin.', $exception)
      );
      return TRUE;
    }
    return FALSE;
  }

}
```