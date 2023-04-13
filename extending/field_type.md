# Extending Field Type

A Field Type is a Drupal field. You need to tell Drupal what data to return for your field.

## Create a new plugin

In this Example, you will pretend you have a contrib module called **dingo** that has added a new field type `dingo_field`.
You have that field on your entity, and want to display it in GraphQL.

Create a new module in your project.

In your module, create the path `src/Plugin/GraphQLCompose/FieldType/` and create a new file `DingoItem.php`.

## Simple value

If your field only needs to get `$item->value` and returns a simple scalar like String, Boolean, Int, you can just use `FieldProducerTrait`. Done.

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
 *   id = "dingo_field",
 *   type_sdl = "String",
 * )
 */
class DingoItem extends GraphQLComposeFieldTypeBase {

  use FieldProducerTrait;

}

```

The annotation under `GraphQLComposeFieldType` tells us two things.

- **id**: The name of the field you are trying to extend.
- **type_sdl**: The type it will become in GraphQL.

## Complex value

If your new field has more complex data, like a list of values, you can change the `type_sdl` to a custom type and implement the `FieldProducerItemInterface` interface.

This allows us to override the value, and return an array of data to be passed to a new **type_sdl** of `Dingo`

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
 *   id = "dingo_field",
 *   type_sdl = "Dingo",
 * )
 */
class DingoItem extends GraphQLComposeFieldTypeBase implements FieldProducerItemInterface {

  use FieldProducerTrait;

  /**
   * {@inheritdoc}
   */
  public function resolveFieldItem(FieldItemInterface $item, array $context, RefinableCacheableDependencyInterface $metadata) {
    if (!$item->value) {
      return;
    }

    return [
      'dingo' => true,
      'bingo' => $item->value,
      'woof' => "Dingo don't woof"
    ];
  }
}
```

Now you have to create a [Custom Schema Type](extending/schema_type.md) to handle the `Dingo` data you are returning.
