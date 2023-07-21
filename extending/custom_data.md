# Custom data

> This documentation is updated as questions arise in issues or on Slack.

Extended examples can be found in `graphql_compose.apl.php`

## Change a field's data

How can I change the value of a field before it is returned in the query?

---

The easiest option is to use a result alter hook.

```php
<?php

/**
 * @file
 * My drupal module that extends graphql_compose.
 */

declare(strict_types=1);

use Drupal\Core\Cache\RefinableCacheableDependencyInterface;
use Drupal\Core\Field\FieldItemListInterface;

/**
 * Implements hook_graphql_compose_field_results_alter().
 *
 * Modify the entity results as they are given to the schema.
 */
function mymodule_graphql_compose_field_results_alter(array &$results, array $context, RefinableCacheableDependencyInterface $metadata): void {

  // Check the value is a field item list.
  $field_list = $context['value'] ?? NULL;
  if (!$field_list instanceof FieldItemListInterface) {
    return;
  }

  // Get the entity and field definition off the field list item.
  $entity = $field_list->getEntity();
  $field = $field_list->getFieldDefinition();

  // Replace data in the results array.
  if ($entity->getEntityTypeId() ==='node' && $field->getName() === 'field_potato') {
    $results = ['new node value for field_potato'];
  }
}
```

## Q: Add a computed field to an entity

**Question**: Is it possible for a field with multiple paragraphs in it to be able to get the position of these paragraphs individually? I'm building an API and the order of these paragraphs should be visible in the queries.

---

The weight for a Paragraph isn't actually a field or a value. We could add a computed field using Drupal's base field API, and set that data as the delta.

<!-- tabs:start -->

### **mymodule.module**

```php
<?php

/**
 * @file
 * My drupal module that extends graphql_compose.
 */

declare(strict_types=1);

use Drupal\Core\Cache\RefinableCacheableDependencyInterface;
use Drupal\Core\Entity\EntityTypeInterface;
use Drupal\Core\Field\BaseFieldDefinition;
use Drupal\paragraphs\ParagraphInterface;

/**
 * Implements hook_entity_base_field_info().
 *
 * Add a weight field to paragraphs.
 */
function mymodule_entity_base_field_info(EntityTypeInterface $entity_type): ?array {
  if ($entity_type->id() === 'paragraph') {
    $fields = [];
    $fields['weight'] = BaseFieldDefinition::create('integer')
      ->setLabel(t('Weight'))
      ->setDescription(t('The delta weight of this paragraph within a fielded context.'))
      ->setComputed(TRUE)
      ->setDefaultValue(0);
    return $fields;
  }
}

/**
 * Implements hook_graphql_compose_field_results_alter().
 *
 * Modify the entity results as they are given to the schema.
 *
 * You could also compute this somewhere else, but for this example
 * we'll do it here.
 *
 * The delta is the position of the paragraph in the field.
 */
function mymodule_graphql_compose_field_results_alter(array &$results, array $context, RefinableCacheableDependencyInterface $metadata): void {
  foreach ($results as $delta => $result) {
    if ($result instanceof ParagraphInterface) {
      $result->set('weight', $delta);
    }
  }
}

/**
 * Implements hook_graphql_compose_entity_base_fields_alter().
 *
 * Add the new "weight" field to the interface.
 */
function mymodule_graphql_compose_entity_base_fields_alter(array &$fields, string $entity_type_id): void {
  if ($entity_type_id === 'paragraph') {
    // You could use any entity type base_field annotation in the array.
    // @see https://drupal-graphql-compose.github.io/documentation/#/extending/entity_type
    $fields['weight'] = [];
  }
}
```

### **Resulting schema**

```graphql
"""
Entity type paragraph.
"""
interface ParagraphInterface {
  """
  The Universally Unique IDentifier (UUID).
  """
  id: ID!

  """
  The time that the Paragraph was created.
  """
  created: DateTime!

  """
  The paragraphs entity language code.
  """
  langcode: Language!

  """
  The weight of this paragraph within a fieldable context.
  """
  weight: Int!
}
```

### **Example query**

```graphql
{
  route(path: "/node/123") {
    ... on RouteInternal {
      entity {
        ... on NodeArticle {
          content {
            ... on ParagraphInterface {
              id
              weight
            }
          }
        }
      }
    }
  }
}
```

### **Example result**

```json
{
  "data": {
    "route": {
      "entity": {
        "content": [
          {
            "id": "12126720-a693-4f5a-ad8c-fc9a2d2207f6",
            "weight": 0
          },
          {
            "id": "99fb720b-2c25-4c18-a557-8c94153f31bb",
            "weight": 1
          },
          {
            "id": "dc5d099f-d5d8-4415-8a5d-0a4d713eb30c",
            "weight": 2
          },
          {
            "id": "dd49ac16-4940-4420-8c3b-2f50cd4e4006",
            "weight": 3
          },
          {
            "id": "c26fed87-f8fd-4592-b5f8-fc9bc5ffb8da",
            "weight": 4
          },
          {
            "id": "84cab540-f739-4d07-aa8c-c613729d0125",
            "weight": 5
          }
        ]
      }
    }
  }
}
```

<!-- tabs:end -->
