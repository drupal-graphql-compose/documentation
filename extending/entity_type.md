# Entity Types

> :baby: New entity types are added to the schema by exposing them as plugins.

## Create a new plugin

In your module, create the path `my_module/src/Plugin/GraphQLCompose/EntityType/MyEntity.php`.

Example:

- Entity type: `MyEntity`
- Entity type ID: `my_entity`

```php
<?php

declare(strict_types=1);

namespace Drupal\my_module\Plugin\GraphQLCompose\EntityType;

use Drupal\my_module\Plugin\GraphQLCompose\GraphQLComposeEntityTypeBase;

/**
 * Define entity type.
 *
 * @GraphQLComposeEntityType(
 *   id = "my_entity",
 *   prefix = "MyEntity",
 *   base_fields = {
 *     "title" = {
 *       "field_type" = "entity_label",
 *     },
 *     "langcode" = {},
 *     "created" = {},
 *     "changed" = {},
 *   }
 * )
 */
class MyEntity extends GraphQLComposeEntityTypeBase {

}
```

This will enable the `MyEntity` entity type, and create types such as `MyEntityInterface`, `MyEntityTypeA`, `MyEntityTypeAbc`.

> ### A good chance to override
>
> Two methods of interest on the `GraphQLComposeEntityTypeBase` class are `registerTypes` and `registerResolvers`. These two methods control how your entity is exposed and served to the schema. You have complete control to override these methods.

## Interface fields

The annotation property `base_fields` controls which fields are added to the interface. These are the fields that are on every entity type. These fields will become your interface.

You can find which fields are on your entity by checking the Entity definition in PHP and looking for the `BaseFieldDefinition` properties.

## Customising the fields

You can override the fields by adding properties to their declaration in base_fields.

| Annotation property | Type      | Default     | Note                                                                   |
| ------------------- | --------- | ----------- | ---------------------------------------------------------------------- |
| `field_type`        | `string`  | `automatic` | The field type used to resolve the field. Maps to a `FieldType` plugin |
| `name_sdl`          | `string`  | `automatic` | The field name used within the GraphQL schema                          |
| `type_sdl`          | `string`  | `automatic` | The field type used withing the schema. Maps to a `SchemaType` plugin. |
| `description`       | `string`  | `automatic` | The field description used withing the schema.                         |
| `required`          | `boolean` | `TRUE`      | Interface fields are usually required. Optionally disable.             |
| `multiple`          | `boolean` | `automatic` | Yes                                                                    |

## Extending interface fields

If you want to modify interface fields outside of the plugin, you can use the `hook_graphql_compose_entity_base_fields_alter` hook.

```php
function hook_graphql_compose_entity_base_fields_alter(array &$fields, string $entity_type_id) {
  if ($entity_type_id === 'commerce_order_item') {
    $fields['license'] = [
      'type_sdl' => 'MyLicenseType'
    ];
  }
}
```
