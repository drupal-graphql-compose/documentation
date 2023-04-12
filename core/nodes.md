# Nodes

- Visit `/admin/structure/types`
- Next to the type you want to expose: Select `Edit`
- Down the bottom select the `GraphQL` tab
- Click `Enable GraphQL`

## GraphQL Types

Node entities are exposed to GraphQL with the interface `NodeInterface` and the union `NodeUnion`.

## Load node type by UUID

> It's recommended you load your nodes using the [Routes](features/routes.md) feature if the UUID is unknown.

Each node type has the option to `Enable single query`. The node's machine name is converted into camel case and prefixed with "node". For example, the `Article` node type will have a single query of `nodeArticle`.

```graphql
{
  nodeArticle(id: "uuid-of-your-node") {
    id
    title
  }
}
```

> Extra loading options are available when features such as `Route` and `Edges` are enabled.
