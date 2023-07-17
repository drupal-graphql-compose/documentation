# Field Types

> A Field Type is a Drupal field. You need to tell Drupal what data to return for your field.\
> A Drupal field type Example is: `Drupal\text\Plugin\Field\FieldType\TextWithSummaryItem`

## Option: Modify the result with hooks

If you want to modify the result of an existing field type, you can use hooks.

```php
use \Drupal\Core\Cache\RefinableCacheableDependencyInterface;

/**
 * Alter results for producers which use FieldProducerPluginBase.
 *
 * @param array $results
 *   The results being returned.
 * @param array $context
 *   Context Passed to resolver. Eg $context['field'].
 * @param \Drupal\Core\Cache\RefinableCacheableDependencyInterface $metadata
 *   Context for metadata expansion.
 */
function hook_graphql_compose_field_results_alter(array &$results, array $context, RefinableCacheableDependencyInterface $metadata) {
  $field = $context['value']->getFieldDefinition();
  if ($field->getName() === 'field_potato') {
    foreach ($results as &$result) {
      $result['chips'] = TRUE;
    }
  }
}
```

## Option: Replace the plugin

If you're a savvy developer, you can override or modify a schema type by altering the plugin definition.

```php
/**
 * Implements hook_graphql_compose_field_type_alter().
 */
function mymodule_graphql_compose_field_type_alter(array &$field_types) {
  $field_types['address']->setClass('Drupal\mymodule\MyAddressItem');
}
```

```php
<?php

declare(strict_types=1);

namespace Drupal\mymodule;

use Drupal\graphql_compose\Plugin\GraphQLCompose\FieldType\AddressItem;
use Drupal\Core\Cache\RefinableCacheableDependencyInterface;
use Drupal\Core\Field\FieldItemInterface;

/**
 * My custom field override.
 */
class MyAddressItem extends AddressItem {

  public function resolveFieldItem(FieldItemInterface $item, array $context, RefinableCacheableDependencyInterface $metadata) {
    $result = parent::resolveFieldItem($item, $context, $metadata);

    if (!is_null($result)) {
      $result['cool'] = TRUE;
    }

    return $result;
  }
}
```

## Option: Create a new plugin

In your module:

- Create the path `src/Plugin/GraphQLCompose/FieldType/`
- Create a new file `MyFieldType.php`.

### Annotation properties

| Property      | Type   | Note                                                                                                |
| ------------- | ------ | --------------------------------------------------------------------------------------------------- |
| `id`          | string | This should match the id of your @FieldType definition. Eg: TextWithSummaryItem = text_with_summary |
| `type_sdl`    | string | The GraphQL type your field value will be. Maps to a `SchemaType` plugin.                           |
| `description` | string | (Optional) You can set a default description for your field within the schema.                      |

### Simple field value

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
}
```

If your field only needs to get `$item->value` and returns a simple scalar like String, Boolean, Int, you can just use `FieldProducerTrait`.

### Complex field value

If your new field has more complex data, like a list of values:

1. Change the `type_sdl` to a custom type
2. Implement the `FieldProducerItemInterface` interface.
3. Extend or replace the `resolveFieldItem` method.

This example produces an array value for a _my_complex_field_ field.

```php
<?php

declare(strict_types=1);

namespace Drupal\my_module\Plugin\GraphQLCompose\FieldType;

use Drupal\Core\Cache\RefinableCacheableDependencyInterface;
use Drupal\Core\Field\FieldItemInterface;
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
  public function resolveFieldItem(FieldItemInterface $item, array $context, RefinableCacheableDependencyInterface $metadata) {
    return [
      'label' => $item->getLabel(),
      'foos' => $item->getBars(),
    ];
  }
}
```

Now you need to create a [Custom Schema Type](extending/schema_type.md) to handle the `MyComplexType` data you are returning.
