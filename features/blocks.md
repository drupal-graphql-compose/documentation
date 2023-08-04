# Blocks

> :fire: Enable the **graphql_compose_blocks** module.

Block Content are an entity with a type, which will follow the core entity pattern.

- Visit GraphQL Compose &rarr; Schema at `/admin/config/graphql_compose`
- Select the `Custom block` tab
- Select the entity bundle you want to enable.
- Click `Enable GraphQL`
- [Add some fields](core/fields.md).

> :thinking: GraphQL Compose supports the [block_field](https://www.drupal.org/project/block_field) module. You can use it load blocks by reference in a field.

## Blocks

### BlockInterface

```graphql
"""
Blocks are a modular piece of content that can be displayed in various regions of a website's layout.
"""
interface BlockInterface {
  """
  The Universally Unique IDentifier (UUID).
  """
  id: ID!

  """
  The title of the block if provided.
  """
  title: String

  """
  The rendered output of the block.
  """
  render: Html
}
```

## BlockPlugin

Plugin blocks are considered _scalar unknown weird things_.
You don't know whats in them. Only that they probably have content.

The best you can do is get a render.

<!-- tabs:start -->

### **Query**

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

### **BlockPlugin**

```graphql
"""
A generic block plugin is a modular piece of content that can be displayed in various regions of a website's layout.
"""
type BlockPlugin implements BlockInterface {
  """
  The Universally Unique IDentifier (UUID).
  """
  id: ID!

  """
  The title of the block if provided.
  """
  title: String

  """
  The rendered output of the block.
  """
  render: Html
}
```

<!-- tabs:end -->

## BlockContent

<!-- tabs:start -->

### **Query**

```graphql
{
  block(id: "block_content:43267584-d965-11ed-afa1-0242ac120002") {
    ... on BlockContent {
      title
      entity {
        ... on BlockContentBasic {
          body {
            processed
          }
        }
      }
    }
  }
}
```

### **BlockContent**

```graphql
"""
Block content is a modular piece of content that can be displayed in various regions of a website's layout.
"""
type BlockContent implements BlockInterface {
  """
  The Universally Unique IDentifier (UUID).
  """
  id: ID!

  """
  The title of the block if provided.
  """
  title: String

  """
  The rendered output of the block.
  """
  render: Html

  """
  The Content Block entity to be displayed within the block.
  """
  entity: BlockContentUnion!
}
```

### **BlockContentInterface**

`BlockContentUnion` &rarr; `BlockContentInterface`

```graphql
"""
Entity type block_content.
"""
interface BlockContentInterface {
  """
  The Universally Unique IDentifier (UUID).
  """
  id: ID!

  """
  The time that the content block was last edited.
  """
  changed: DateTime!

  """
  The content block language code.
  """
  langcode: Language!

  """
  A boolean indicating whether this block is reusable.
  """
  reusable: Boolean!

  """
  A brief description of your block.
  """
  title: String!
}
```

<!-- tabs:end -->

### Block Content GraphQL Types

Block Content entities are exposed to GraphQL with the interface `BlockContentInterface` and the union `BlockContentUnion`.

Each enabled block content type has a schema type. For example, the `basic` block type will be typed as `BlockContentBasic`.

## BlockField

BlockFields are documented within the [Layout Builder](features/layout-builder.md?id=blockfield) documentation.
