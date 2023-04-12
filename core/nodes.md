# Nodes

- Visit `/admin/structure/types`
- Next to the type you want to expose: Select `Edit`
- Down the bottom select the `GraphQL` tab
- Click `Enable GraphQL`

## GraphQL Types

Node entities are exposed to GraphQL with the interface `NodeInterface` and the union `NodeUnion`.

Each node type is will have a schema type. For example, the `article` node type will be typed as `NodeArticle`.

## Load node type by UUID

Each node type has the option to `Enable single query`.

The node's schema type name is used to name the query. For example, an `article` node type will have a single query of `nodeArticle`.

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
