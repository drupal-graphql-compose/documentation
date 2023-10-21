# Field Types

> A Field Type is a Drupal field. You need to tell Drupal what data to return for your field.\
> A Drupal field type Example is: `Drupal\text\Plugin\Field\FieldType\TextWithSummaryItem`

## Option: Modify the result

If you want to modify the result of an existing field type, or add new fields to your interface, you can use hooks.

Check out [Custom data](extending/custom_data.md) for more information.

## Option: Custom field type

To create a new plugin, add a class within your module: `src/Plugin/GraphQLCompose/FieldType/`

### Simple, single field value

If your field only needs to get one value from a `FieldItemInterface`, this is sufficient.

```php
<?php

declare(strict_types=1);

namespace Drupal\my_module\Plugin\GraphQLCompose\FieldType;

use Drupal\graphql_compose\Plugin\GraphQLCompose\GraphQLComposeFieldTypeBase;
use Drupal\graphql_compose\Plugin\GraphQL\DataProducer\FieldProducerTrait;

/**
 * {@inheritDoc}
 *
 * @GraphQLComposeFieldType(
 *   id = "my_field_type",
 *   type_sdl = "String",
 * )
 */
class MyFieldType extends GraphQLComposeFieldTypeBase {

  use FieldProducerTrait;

  /**
   * Value to return to getProducerProperty in FieldProducerTrait.
   *
   * This could be value, entity, something_id, whatever.
   * It's equivalent to $entity->field_abc->value
   *
   * @var string
   */
  public $producerProperty = 'value';
}
```

### More complex field value

<!-- tabs:start -->

### **Override field item**

1. Add the `FieldProducerItemInterface` interface.
2. Extend or replace the `resolveFieldItem` method.

This example produces an array value for a my_complex_field field.

```php
<?php

declare(strict_types=1);

namespace Drupal\my_module\Plugin\GraphQLCompose\FieldType;

use Drupal\Core\Field\FieldItemInterface;
use Drupal\graphql\GraphQL\Execution\FieldContext;
use Drupal\graphql_compose\Plugin\GraphQLCompose\GraphQLComposeFieldTypeBase;
use Drupal\graphql_compose\Plugin\GraphQL\DataProducer\FieldProducerItemInterface;
use Drupal\graphql_compose\Plugin\GraphQL\DataProducer\FieldProducerTrait;

/**
 * {@inheritDoc}
 *
 * @GraphQLComposeFieldType(
 *   id = "my_complex_field",
 *   type_sdl = "MyComplexType",
 * )
 */
class MyComplexFieldItem extends GraphQLComposeFieldTypeBase implements FieldProducerItemInterface {

  use FieldProducerTrait;

  /**
   * {@inheritdoc}
   */
  public function resolveFieldItem(FieldItemInterface $item, FieldContext $context) {
    return [
      'label' => $item->getLabel(),
      'foos' => $item->getBars(),
    ];
  }
}
```

### **Override field items**

1. Add the `FieldProducerItemsInterface` interface.
2. Extend or replace the `resolveFieldItems` method.

```php
<?php

declare(strict_types=1);

namespace Drupal\my_module\Plugin\GraphQLCompose\FieldType;

use Drupal\Core\Field\FieldItemInterface;
use Drupal\Core\Field\FieldItemListInterface;
use Drupal\graphql\GraphQL\Execution\FieldContext;
use Drupal\graphql_compose\Plugin\GraphQLCompose\GraphQLComposeFieldTypeBase;
use Drupal\graphql_compose\Plugin\GraphQL\DataProducer\FieldProducerItemInterface;
use Drupal\graphql_compose\Plugin\GraphQL\DataProducer\FieldProducerItemsInterface;
use Drupal\graphql_compose\Plugin\GraphQL\DataProducer\FieldProducerTrait;


/**
 * {@inheritDoc}
 *
 * @GraphQLComposeFieldType(
 *   id = "my_complex_field",
 *   type_sdl = "MyComplexType",
 * )
 */
class MyComplexFieldItem extends GraphQLComposeFieldTypeBase implements FieldProducerItemInterface, FieldProducerItemsInterface {

  use FieldProducerTrait;

  /**
   * {@inheritdoc}
   */
  public function resolveFieldItems(FieldItemListInterface $items, FieldContext $context): array {
    $results = [];
    foreach ($items as $item) {
      $result[] = $this->resolveFieldItem($item, $context);
    }
    return $results;
  }

  /**
   * {@inheritdoc}
   */
  public function resolveFieldItem(FieldItemInterface $item, FieldContext $context) {
    return [
      'label' => $item->getLabel(),
      'foos' => $item->getBars(),
    ];
  }
}
```

### **Override field producer**

1. Use `FieldProducerTrait::getProducers()` as a reference. Remove the trait from your plugin. Do whatever you want.

```php
/**
 * Retrieves the producers for a field.
 *
 * @param \Drupal\graphql\GraphQL\ResolverBuilder $builder
 *   The resolver builder.
 *
 * @return \Drupal\graphql\GraphQL\Resolver\Composite
 *   The composite resolver.
 */
public function getProducers(ResolverBuilder $builder): Composite {
  return $builder->compose(
    $builder->produce('field')
      ->map('entity', $builder->fromParent())
      ->map('field', $builder->fromValue($this->getFieldName())),

    // Useful for extending this producer.
    $builder->context('field_value', $builder->fromParent()),

    // Get the field items.
    $builder->produce('field_producer_plugin')
      ->map('plugin', $builder->fromValue($this))
      ->map('value', $builder->fromParent()),
  );
}
```

<!-- tabs:end -->

Now you need to create a [Custom Schema Type](extending/schema_type.md) to handle the `MyComplexType` data you are returning.

### Annotation properties

| Property      | Type   | Note                                                                                                |
| ------------- | ------ | --------------------------------------------------------------------------------------------------- |
| `id`          | string | This should match the id of your @FieldType definition. Eg: TextWithSummaryItem = text_with_summary |
| `type_sdl`    | string | The GraphQL type your field value will be. Maps to a `SchemaType` plugin.                           |
| `description` | string | (Optional) You can set a default description for your field within the schema.                      |

## Option: Replace the plugin

If you're a savvy developer, you can override or modify a schema type by altering the plugin definition.

```php
/**
 * Implements hook_graphql_compose_field_type_alter().
 */
function mymodule_graphql_compose_field_type_alter(array &$field_types) {
  $field_types['address']['class'] = 'Drupal\mymodule\MyAddressItem';
}
```

```php
<?php

declare(strict_types=1);

namespace Drupal\mymodule;

use Drupal\Core\Field\FieldItemInterface;
use Drupal\graphql\GraphQL\Execution\FieldContext;
use Drupal\graphql_compose\Plugin\GraphQLCompose\FieldType\AddressItem;

/**
 * My custom field override.
 */
class MyAddressItem extends AddressItem {

  public function resolveFieldItem(FieldItemInterface $item, FieldContext $context) {
    $result = parent::resolveFieldItem($item, $context);

    if (!is_null($result)) {
      $result['cool'] = TRUE;
    }

    return $result;
  }
}
```
