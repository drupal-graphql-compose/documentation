# Nodes

- Navigate to the `GraphQL Compose Settings` page at `/admin/config/graphql_compose`
- Select the `Content` tab
- Select the entity bundle you want to enable.
- Click `Enable GraphQL`
- [Add some fields](core/fields.md).

## GraphQL Types

Node entities are exposed to GraphQL with the interface `NodeInterface` and the union `NodeUnion`.

Each enabled node type has a schema type. For example, the `article` node type will be typed as `NodeArticle`.

## Load node type by UUID

Each node type has the option to `Enable single query`.

The node's schema type name is used to name the query. For example, an `article` node type will have a query of `nodeArticle`.

```graphql
{
  nodeArticle(id: "43267052-d965-11ed-afa1-0242ac120002") {
    id
    title
  }
}
```

> It's recommended you load your nodes using the [Routes](features/routes.md) feature if the UUID is unknown.
> Extra loading options are available when features such as `Views`, `Route` and `Edges` are enabled.

> Tip: Don't name your content type "Union" or "Interface" :poop:
