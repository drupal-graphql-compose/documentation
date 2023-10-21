# Custom Data

> :partying_face: You have entered developer land. Now it's no-no-code. You code.

Extended examples can be found in `graphql_compose.apl.php`

This documentation is updated as questions arise in issues or on Slack. Send us your questions!

## Change a field's data

**Question**: How can I change the value of a field before it is returned in the query?

**Answer**: The easiest option is to use a result alter hook. Here's an excessive example:

```php
use Drupal\graphql\GraphQL\Execution\FieldContext;
use Drupal\graphql_compose\Plugin\GraphQLCompose\GraphQLComposeFieldTypeInterface;

/**
 * Alter results for GraphQL Compose producers.
 *
 * @param array $results
 *   The results being returned.
 * @param mixed $entity
 *   The entity being resolved from.
 * @param \Drupal\graphql_compose\Plugin\GraphQLCompose\GraphQLComposeFieldTypeInterface $plugin
 *   The GraphQL Compose field plugin currently being resolved.
 * @param \Drupal\graphql\GraphQL\Execution\FieldContext $context
 *   Context for the current resolution.
 */
function hook_graphql_compose_field_results_alter(array &$results, $entity, GraphQLComposeFieldTypeInterface $plugin, FieldContext $context) {
  $field_definition = $plugin->getFieldDefinition();

  $field_name = $field_definition->getName();
  $entity_type = $field_definition->getTargetEntityTypeId();

  // Replace the results.
  if ($entity_type === 'node' &&  $field_name === 'field_potato') {
    $results = ['new node value for field_potato'];
  }

  // The actual entity for the field being resolved.
  if ($entity?->id() === '123') {
    $results = ['This is node 123'];
  }
}
```

## Add a computed field to an entity

**Question**: Is it possible for a field with multiple paragraphs in it to be able to get the position of these paragraphs individually? I'm building an API and the order of these paragraphs should be visible in the queries.

**Answer**: The weight for a Paragraph isn't actually a field or a value. We could add a computed field using Drupal's base field API, and set that data as the delta.

<!-- tabs:start -->

### **mymodule.module**

```php
use Drupal\Core\Entity\EntityTypeInterface;
use Drupal\Core\Field\BaseFieldDefinition;
use Drupal\graphql\GraphQL\Execution\FieldContext;
use Drupal\graphql_compose\Plugin\GraphQLCompose\GraphQLComposeFieldTypeInterface;
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
function mymodule_graphql_compose_field_results_alter(array &$results, $entity, GraphQLComposeFieldTypeInterface $plugin, FieldContext $context) {
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
