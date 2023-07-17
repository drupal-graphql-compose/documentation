# Entity Types

> As we use plugins, you can override mostly anything. Have it your way. 🍔.

## Create a new plugin

For this example, we'll add a custom entity type "settings" that has bundles.

Create a new module in your project.

In your module, create the path `src/Plugin/GraphQLCompose/EntityType/` and create a new file `Settings.php`.

```php
<?php

declare(strict_types=1);

namespace Drupal\my_module\Plugin\GraphQLCompose\EntityType;

use Drupal\my_module\Plugin\GraphQLCompose\GraphQLComposeEntityTypeBase;

/**
 * Define entity type.
 *
 * @GraphQLComposeEntityType(
 *   id = "settings",
 *   prefix = "Settings",
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

This will enable the `Settings` entity type, and create types such as `SettingsHelloWorld` and `SettingsSomethingElse`.

## Interface fields

The trick here is to add `base_fields`. These are the fields that are on every entity type. These fields will become your interface.

You can find which fields are on your entity by checking the Entity definition in PHP and looking for the `BaseFieldDefinition` properties.

## Customising the fields

You can override the fields by adding properties to their declaration in base_fields.

Properties are:

| Property      | Type    | Default     | Note                                                                   |
| ------------- | ------- | ----------- | ---------------------------------------------------------------------- |
| `field_type`  | string  | `automatic` | The field type used to resolve the field. Maps to a `FieldType` plugin |
| `name_sdl`    | string  | `automatic` | The field name used within the GraphQL schema                          |
| `type_sdl`    | string  | `automatic` | The field type used withing the schema. Maps to a `SchemaType` plugin. |
| `description` | string  | `automatic` | The field description used withing the schema.                         |
| `required`    | boolean | `TRUE`      | Interface fields are usually required. Optionally disable.             |
| `multiple`    | boolean | `automatic` | Yes                                                                    |
