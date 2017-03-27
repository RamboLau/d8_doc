###1、什么是上下文？
"上下文Context"这个词是什么意思呢？平常生活中它常见于语言、文字交流里面，意思是当前交流处于一个特定的环境下，依托前面的内容交流才有意义。

比如这句话:"他正在学习drupal"，如果单独说是没有意义的，因为你不知道"他"指代谁，在交流中前面一定定义清楚了"他"是谁，这个"他"就是上下文，这个谁就是上下文的值。

在软件工程中，上下文是一种属性的有序序列，它们为驻留在环境内的对象定义环境。不过你无需去理会这样晦涩的定义，只需要知道"上下文"相当于"环境"就行了，它们是等价的。

假设将来能制造出真正的类人智能机器人，那么把它投放到社会中，激活那一刻，他第一件事情就是侦查环境，换句话说就是搞清楚自己所在的上下文，然后他才能有所行动。

可见上下文概念是如此重要，在脑子里面建立一个印象：有目的的行为是建立在环境之上的，万事万物皆是如此.

 
在Drupal 8中上下文就是指当请求到来时，系统所处的工作环境，这个环境由请求和系统设置共同构成，系统首先要搞清楚环境（上下文）才知道自己该怎么行动（正应前文所讲）。

比如缓存上下文CacheContext，就是指相对于缓存系统的环境（缓存环境是系统环境的子集），缓存系统依据此环境才能正确行动，具体实现就是缓存依据这个上下文来存放或取回正确的数据。

###2、插件上下文
通常，插件上下文我们放在目录src/Plugin/Condition下。

下面例子我们实现了一个NodeType类，它的上下文是node实体，只有在node实体被调用的情况下它才生效。

core/modules/node/src/Plugin/Condition/NodeType.php代码如下：

```php
<?php

namespace Drupal\node\Plugin\Condition;

use Drupal\Core\Condition\ConditionPluginBase;
use Drupal\Core\Entity\EntityStorageInterface;
use Drupal\Core\Form\FormStateInterface;
use Drupal\Core\Plugin\ContainerFactoryPluginInterface;
use Symfony\Component\DependencyInjection\ContainerInterface;

/**
 * Provides a 'Node Type' condition.
 *
 * @Condition(
 *   id = "node_type",
 *   label = @Translation("Node Bundle"),
 *   context = {
 *     "node" = @ContextDefinition("entity:node", label = @Translation("Node"))
 *   }
 * )
 */
class NodeType extends ConditionPluginBase implements ContainerFactoryPluginInterface {

  /**
   * The entity storage.
   *
   * @var \Drupal\Core\Entity\EntityStorageInterface
   */
  protected $entityStorage;

  /**
   * Creates a new NodeType instance.
   *
   * @param \Drupal\Core\Entity\EntityStorageInterface $entity_storage
   *   The entity storage.
   * @param array $configuration
   *   The plugin configuration, i.e. an array with configuration values keyed
   *   by configuration option name. The special key 'context' may be used to
   *   initialize the defined contexts by setting it to an array of context
   *   values keyed by context names.
   * @param string $plugin_id
   *   The plugin_id for the plugin instance.
   * @param mixed $plugin_definition
   *   The plugin implementation definition.
   */
  public function __construct(EntityStorageInterface $entity_storage, array $configuration, $plugin_id, $plugin_definition) {
    parent::__construct($configuration, $plugin_id, $plugin_definition);
    $this->entityStorage = $entity_storage;
  }

  /**
   * {@inheritdoc}
   */
  public static function create(ContainerInterface $container, array $configuration, $plugin_id, $plugin_definition) {
    return new static(
      $container->get('entity.manager')->getStorage('node_type'),
      $configuration,
      $plugin_id,
      $plugin_definition
    );
  }

  /**
   * {@inheritdoc}
   */
  public function buildConfigurationForm(array $form, FormStateInterface $form_state) {
    $options = array();
    $node_types = $this->entityStorage->loadMultiple();
    foreach ($node_types as $type) {
      $options[$type->id()] = $type->label();
    }
    $form['bundles'] = array(
      '#title' => $this->t('Node types'),
      '#type' => 'checkboxes',
      '#options' => $options,
      '#default_value' => $this->configuration['bundles'],
    );
    return parent::buildConfigurationForm($form, $form_state);
  }

  /**
   * {@inheritdoc}
   */
  public function submitConfigurationForm(array &$form, FormStateInterface $form_state) {
    $this->configuration['bundles'] = array_filter($form_state->getValue('bundles'));
    parent::submitConfigurationForm($form, $form_state);
  }

  /**
   * {@inheritdoc}
   */
  public function summary() {
    if (count($this->configuration['bundles']) > 1) {
      $bundles = $this->configuration['bundles'];
      $last = array_pop($bundles);
      $bundles = implode(', ', $bundles);
      return $this->t('The node bundle is @bundles or @last', array('@bundles' => $bundles, '@last' => $last));
    }
    $bundle = reset($this->configuration['bundles']);
    return $this->t('The node bundle is @bundle', array('@bundle' => $bundle));
  }

  /**
   * {@inheritdoc}
   */
  public function evaluate() {
    if (empty($this->configuration['bundles']) && !$this->isNegated()) {
      return TRUE;
    }
    $node = $this->getContextValue('node');
    return !empty($this->configuration['bundles'][$node->getType()]);
  }

  /**
   * {@inheritdoc}
   */
  public function defaultConfiguration() {
    return array('bundles' => array()) + parent::defaultConfiguration();
  }

}
```

