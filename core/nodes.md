# Nodes

- Navigate to the `GraphQL Compose Settings` page at `/admin/config/graphql_compose`
- Select the `Content` tab
- Select the entity bundle you want to enable.
- Click `Enable GraphQL`
- [Add some fields](core/fields.md).

<!-- tabs:start -->

### **Example Query**

Each node type has the option to `Enable Single Query`.

The node's schema type name is used to name the query. For example, an `article` node type will have a query of `nodeArticle`.

```graphql
nodeArticle(id: "43267052-d965-11ed-afa1-0242ac120002") {
  id
  title
}
```

### **Result**

```json
{
  "data": {
    "nodeArticle": {
      "id": "43267052-d965-11ed-afa1-0242ac120002",
      "title": "Hello World"
    }
  }
}
```

### **NodeInterface**

```graphql
"""
Entity type node.
"""
interface NodeInterface {
  """
  The Universally Unique IDentifier (UUID).
  """
  id: ID!

  """
  The username of the content author.
  """
  author: User!

  """
  The time that the node was last edited.
  """
  changed: DateTime!

  """
  The time that the node was created.
  """
  created: DateTime!

  """
  Language
  """
  langcode: Language!

  """
  URL alias
  """
  path: String!

  """
  Promoted to front page
  """
  promote: Boolean!

  """
  Published
  """
  status: Boolean!

  """
  Sticky at top of lists
  """
  sticky: Boolean!

  """
  Title
  """
  title: String!
}
```

<!-- tabs:end -->

> You can load nodes using [Routes](features/routes.md).
> Extra loading options are available when features such as `Views`, `Route`, `Menu` and `Edges` are enabled.

> You don't have to use UUIDs if you don't want to. there is options in the settings.

## GraphQL Types

Node entities are exposed to GraphQL with the interface `NodeInterface` and the union `NodeUnion`.

Each enabled node type has a schema type. For example, the `article` node type will be typed as `NodeArticle`.

> Tip: Don't name your content type "Union" or "Interface"
