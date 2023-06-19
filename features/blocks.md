# Blocks

> :fire: Enable the **graphql_compose_blocks** module.

## Block types

### Block Content blocks

Block Content are an entity with a type, which will follow the core entity pattern.

- Navigate to the `GraphQL Compose Settings` page at `/admin/config/graphql_compose`
- Select the `Custom block` tab
- Select the entity bundle you want to enable.
- Click `Enable GraphQL`
- [Add some fields](core/fields.md).

#### GraphQL Types

Block Content entities are exposed to GraphQL with the interface `BlockContentInterface` and the union `BlockContentUnion`.

Each enabled block content type has a schema type. For example, the `cool_beans` block type will be typed as `BlockContentCoolBeans`.

### Plugin blocks

Plugin blocks are considered _scalar unknown weird things_.
You don't know whats in them. Only that they probably have content.

The best you can do is get a render.

## Loading a block

Blocks can be loaded by their system `plugin id`. Block plugin IDs aren't easy. Heres some examples:

### Loading a plugin block

```graphql
{
  block(id: "system_powered_by_block") {
    ... on BlockPlugin {
      title
      render
    }
  }
}
```

### Loading a content block by it's uuid

```graphql
{
  block(id: "block_content:43267584-d965-11ed-afa1-0242ac120002") {
    ... on BlockContent {
      title
      entity {
        ... on BlockContentCoolBeans {
          beans
        }
      }
    }
  }
}
```

> Tip: GraphQL Compose supports the [block_field](https://www.drupal.org/project/block_field) module. You can use it load blocks by reference in a Paragraph or Node.
