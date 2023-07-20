# Custom data

> This documentation is updated as questions arise in issues or on Slack.

## Q: How to add a weight field to paragraphs.

**Question**: Is it possible for a field with multiple paragraphs in it to be able to get the position of these paragraphs individually? I'm building an API and the order of these paragraphs should be visible in the queries.

https://www.drupal.org/project/graphql_compose/issues/3375848

---

The weight for a Paragraph isn't actually a field or a value. We could add the delta as a computed field using Drupal's base field API, and set that data as the weight.

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
      ->setDescription(t('The weight of this paragraph within a fieldable context.'))
      ->setComputed(TRUE)
      ->setDefaultValue(0);
    return $fields;
  }
}

/**
 * Implements hook_graphql_compose_field_results_alter().
 *
 * Modify the entity results as they are given to the schema.
 */
function mymodule_graphql_compose_field_results_alter(array &$results, array $context, RefinableCacheableDependencyInterface $metadata): void {
  foreach ($results as $delta => $result) {
    if ($result instanceof ParagraphInterface) {
      $result->set('weight', $delta ?? 0);
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
  route(path: "/node/1") {
    ... on RouteInternal {
      entity {
        ... on NodePage {
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
