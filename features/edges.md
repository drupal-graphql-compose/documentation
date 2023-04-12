# Connections with Edges

> :fire: Enable the **graphql_compose_edges** module.

## What is it

[Apollo GraphQL](https://www.apollographql.com/blog/graphql/explaining-graphql-connections/) have a good blog article on what they are, but for our purposes we'll simplify it down to: **Load multiple things of a type**.

## Just use Views

If you find the ridigness of the Edge Cursor implementation too restrictive, you can use the [Views](features/views.md) feature instead. It enables filtering, sorting & pagination on any available entity type. Views and Connections could have different use cases, depending on your app.

## Enabling edges

After enabling the feature module, Each entity type has the option to `Enable multiple query`.

The node's schema type name is pluralised to name the query. For example, an `article` node type will have a query of `nodeArticles`.

```graphql
{
  nodeArticles(first: 5) {
    nodes {
      id
    }
  }
}
```

## Cursors

The cursor is a unique identifier for a specific node in the data set. The cursor can be used as a parameter in a query to specify the starting point for the next set of results. Cursors can also be used to implement features like infinite scrolling or backward pagination.

```graphql
{
  nodeArticles(
    first: 5
    after: "khkh23kgi3vj578i2yu3gKUGfej2GHV...sdkbhsdhjagsd="
  ) {
    nodes {
      id
    }
  }
}
```
