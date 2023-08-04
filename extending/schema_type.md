# Schema Types

> :thinking: A Schema Type is what GraphQL will serve you. You need to describe to the schema what your Type is.

There is plenty of ways to extend or define your own schema.

Nothing in these example has a GraphQL Resolver. You'll need to [add your own resolvers](https://drupal-graphql.gitbook.io/graphql/data-producers/producers#add-the-resolver) to get data.

## Option: Use normal GraphQL schema extensions

You don't need to use GraphQL Compose to extend your schema.\
You can use normal [GraphQL 4.x](https://drupal-graphql.gitbook.io/graphql/) to extend or create your own schema types.

## Option: Use the manager

You can use the `GraphQLComposeSchemaTypeManager` to add types directly into the schema. You could do this within your own GraphQL extension.

```php
$myType = new \GraphQL\Type\Definition\ObjectType(...);

$manager = \Drupal::service('graphql_compose.schema_type_manager');
$manager->add($myType);
$extension = $manager->extend($myExtension);
$whatever = $manager->get('MyType');
```

## Option: Use hooks on the manager

You can use hooks to alter the schema types just before we print the schema.

<!-- tabs:start -->

### **Hooks**

```php
<?php

use \Drupal\graphql_compose\Plugin\GraphQLComposeSchemaTypeManager;
use \GraphQL\Type\Definition\Type;
use \GraphQL\Type\Definition\ObjectType;

/**
 * Add custom types to the schema.
 *
 * @param Drupal\graphql_compose\Plugin\GraphQLComposeSchemaTypeManager $manager
 *   The GraphQL Compose Schema Type Manager.
 */
function hook_graphql_compose_print_types(GraphQLComposeSchemaTypeManager $manager): void {
  $my_type = new ObjectType([
    'name' => 'MyType',
    'fields' => [
      'id' => Type::string(),
    ],
  ]);
  $manager->add($my_type);
}

/**
 * Add extensions to the schema.
 *
 * @param Drupal\graphql_compose\Plugin\GraphQLComposeSchemaTypeManager $manager
 *   The GraphQL Compose Schema Type Manager.
 */
function hook_graphql_compose_print_extensions(GraphQLComposeSchemaTypeManager $manager): void {
  $my_extension = new ObjectType([
    'name' => 'Query',
    'fields' => fn() => [
      'thing' => [
        'type' => $manager->get('MyType'),
        'description' => (string) t('Get my type'),
      ],
    ],
  ]);
  $manager->extend($my_extension);
}

```

### **Example Schema**

```graphql
type MyType {
  id: String
}

extend type Query {
  """
  Get my type
  """
  thing: MyType
}
```

<!-- tabs:end -->

## Option: Custom schema type

Create a new GraphQL Schema type of "Customer"

<!-- tabs:start -->

### **Customer**

`src/Plugin/GraphQLCompose/SchemaType/Customer.php`

The annotation within `GraphQLComposeSchemaType` tells us:

- **id**: The name of the type you are trying to create.

```php
<?php

declare(strict_types=1);

namespace Drupal\my_module\Plugin\GraphQLCompose\SchemaType;

use Drupal\graphql_compose\Plugin\GraphQLCompose\GraphQLComposeSchemaTypeBase;
use GraphQL\Type\Definition\ObjectType;
use GraphQL\Type\Definition\Type;

/**
 * {@inheritDoc}
 *
 * @GraphQLComposeSchemaType(
 *   id = "Customer"
 * )
 */
class Customer extends GraphQLComposeSchemaTypeBase {

  /**
   * {@inheritDoc}
   */
  public function getTypes(): array {
    $types = [];

    // Add the "Customer" type.
    $types[] = new ObjectType([
      'name' => $this->getPluginId(),
      'description' => (string) $this->t('A customer.'),
      'fields' => fn() => [
        'name' => Type::string(),
        'email' => static::type('Email'),
        'orders' => Type::listOf(static::type('CustomerOrder')),
        'active' => Type::nonNull(Type::boolean()),
      ],
    ]);

    return $types;
  }

  /**
   * {@inheritDoc}
   */
  public function getExtensions(): array {
    $extensions = parent::getExtensions();

    // Add the "customer" field to the existing "User" type by extending it.
    $extensions[] = new ObjectType([
      'name' => 'User',
      'fields' => fn() => [
        'customer' => static::type('Customer'),
      ],
    ]);

    // Add a "customer" query to the existing "Query" type by extending it.
    $extensions[] = new ObjectType([
      'name' => 'Query',
      'fields' => fn() => [
        'customer' => static::type('Customer')
      ],
    ]);

    return $extensions;
  }
}
```

Within a Schema Type you can use `static::type('...')` to resolve a previously defined type. Eg:

```php
'email' => static::type('Email')
```

### **CustomerOrder**

`src/Plugin/GraphQLCompose/SchemaType/CustomerOrder.php`

We referenced orders in the customer example. So now we can create that type.

```php
<?php

declare(strict_types=1);

namespace Drupal\my_module\Plugin\GraphQLCompose\SchemaType;

use Drupal\graphql_compose\Plugin\GraphQLCompose\GraphQLComposeSchemaTypeBase;
use GraphQL\Type\Definition\ObjectType;
use GraphQL\Type\Definition\Type;

/**
 * {@inheritDoc}
 *
 * @GraphQLComposeSchemaType(
 *   id = "CustomerOrder"
 * )
 */
class CustomerOrder extends GraphQLComposeSchemaTypeBase {

  /**
   * {@inheritDoc}
   */
  public function getTypes(): array {
    $types = [];

    $types[] = new ObjectType([
      'name' => $this->getPluginId(),
      'description' => (string) $this->t('A customer order.'),
      'fields' => fn() => [
        'id'  => Type::id(),
        'amount' => Type::float(),
      ],
    ]);

    return $types;
  }
}
```

> :thinking: Take a look at existing _GraphQLComposeSchemaType_ plugins to see how to chop up your schema. It's pretty flexible.

### **Example Schema**

```graphql
type Customer {
  name: String
  email: Email
  orders: [CustomerOrder]
  active: Boolean!
}

type CustomerOrder {
  id: ID
  amount: Float
}

extend type Query {
  customer: Customer
}

extend type User {
  customer: Customer
}
```

### **Example Query**

```graphql
query {
  # Get the current User.
  viewer {
    # Our new Customer type
    customer {
      name
      email
      # Our new CustomerOrder type
      orders {
        id
        amount
      }
      active
    }
  }

  # Our query extension
  customer {
    name
    email
    orders {
      id
      amount
    }
    active
  }
}
```

<!-- tabs:end -->

## Option: Override GraphQL Compose

If you're a savvy developer, you can override or modify a schema type by altering the plugin definition.

```php
/**
 * Implements hook_graphql_compose_field_type_alter().
 */
function mymodule_graphql_compose_schema_type_alter(array &$entity_types) {
  $entity_types['address']->setClass('Drupal\mymodule\MyAddressSchemaType');
}
```

```php
<?php

namespace Drupal\mymodule;

use Drupal\graphql_compose\Plugin\GraphQLCompose\SchemaType\AddressType;
use GraphQL\Type\Definition\ObjectType;
use GraphQL\Type\Definition\Type;

/**
 * My custom plugin override.
 */
class MyAddressSchemaType extends AddressType {
  public function getExtensions(): array {
    $extensions = parent::getExtensions();

    $extensions[] = new ObjectType([
      'name' => 'Address',
      'fields' => fn() => [
        'cool' => Type::boolean(),
      ],
    ]);

    return $extensions;
  }
}
```
