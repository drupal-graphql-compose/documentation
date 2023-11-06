# Upgrading

## Beta to 2.0.0

### Entity SDL type names are now (optionally) singuarized.

- The language inflector has been changed to use [doctrine/inflector](https://www.doctrine-project.org/projects/doctrine-inflector/en/2.0/index.html) for better language support.
- The entity type names are now optionally singularized.

<!-- tabs:start -->

#### **Now (2.0.0+)**

- Entity type: Taxonomy Term
- Vocabulary name: `Tags`
- SDL Type: `TermTag`
- Single query: `termTag()`
- Edge Query: `termTags()`

A results of this is the name of your types can change. Eg `NodeQuizzes` is now `NodeQuiz`.

Please check your schema and update your queries accordingly.

#### **Now (With singularisation disabled)**

- Entity type: Taxonomy Term
- Vocabulary name: `Tags`
- SDL Type: `TermTags`
- Single query: `termTags()`
- Edge Query: `termTagsItems`

#### **Before (Beta)**

- Entity type: Taxonomy Term
- Vocabulary name: `Tags`
- SDL Type: `TermTags`
- Single query: `termTags()`
- Edge Query: `termTagss`

<!-- tabs:end -->

<!-- tabs:start -->

#### **Now (2.0.0+)**

```php
/**
 * Alter the result from language pluralize.
 *
 * @param string $singular
 *   Singular bundle string to be converted.
 * @param string $plural
 *   Result from the language inflector interface.
 */
function hook_graphql_compose_pluralize_alter($singular, string &$plural): void {
  if ($singular === 'tog') {
    $plural = 'tugs';
  }
}
```

- The pluralize and singularize hooks have been changed.
- New hook `hook_graphql_compose_singularize_alter` added.
- The `$plural` hook argument is now a `string`

#### **Before (Beta)**

```php
/**
 * Alter the result from language pluralize.
 *
 * @param string $original
 *   Original string to be converted.
 * @param array $plural
 *   Result from the language interface.
 */
function hook_graphql_compose_pluralize_alter($original, array &$plural): void {
  if ($singular === 'tog') {
    $plural = ['tugs'];
  }
}
```

<!-- tabs:end -->

### GraphQLComposeFieldType interface changes

- Removal of `array $context` arg.
- Change `RefinableCacheableDependencyInterface` to `FieldContext`
- Renamed graphql producer `field_type_plugin` to `field_producer_plugin`

#### FieldProducerItemInterface

<!-- tabs:start -->

##### **Now (2.0.0+)**

```php
public function resolveFieldItem(
  \Drupal\Core\Field\FieldItemInterface $item,
  \Drupal\graphql\GraphQL\Execution\FieldContext $context
);
```

##### **Before (Beta)**

```php
public function resolveFieldItem(
  \Drupal\Core\Field\FieldItemInterface $item,
  array $context,
  \Drupal\Core\Cache\RefinableCacheableDependencyInterface $metadata
);
```

<!-- tabs:end -->

#### FieldProducerItemsInterface

<!-- tabs:start -->

##### **Now (2.0.0+)**

```php
public function resolveFieldItems(
  \Drupal\Core\Field\FieldItemListInterface $items,
  \Drupal\graphql\GraphQL\Execution\FieldContext $context
);
```

```php
// GraphQL field context can be accessed via the context object.
$banana = $context->getContextValue('banana');
$context->addCacheableDependency($entity);
```

##### **Before (Beta)**

```php
public function resolveFieldItems(
  \Drupal\Core\Field\FieldItemListInterface $items,
  array $context,
  \Drupal\Core\Cache\RefinableCacheableDependencyInterface $metadata
);
```

```php
$banana = $context['banana'];
$metadata->addCacheableDependency($entity);
```

<!-- tabs:end -->

### Hook `hook_graphql_compose_field_results_alter` changes

This has been reworked to give direct context to the field plugin and entity being resolved.

```php
/**
 * Alter results for GraphQL Compose producers.
 *
 * @param array $results
 *   The results being returned.
 * @param mixed $entity
 *   The entity or config being resolved from.
 * @param \Drupal\graphql_compose\Plugin\GraphQLCompose\GraphQLComposeFieldTypeInterface $plugin
 *   The GraphQL Compose field plugin currently being resolved.
 * @param \Drupal\graphql\GraphQL\Execution\FieldContext $context
 *   Context for the current resolution.
 */
function hook_graphql_compose_field_results_alter(
  array &$results,
  $entity,
  GraphQLComposeFieldTypeInterface $plugin,
  FieldContext $context
) {
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
