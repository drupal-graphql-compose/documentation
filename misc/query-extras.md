# Query extras

## Query names

Entity query names are automatically generated using a singularisation/pluralisation of the entity type machine name, this can sometimes give unwanted results.

If you find the pluralisation is wrong, or just bad, you can override it by creating a Drupal module and hooking

- `hook_graphql_compose_singularize_alter()`
- `hook_graphql_compose_pluralize_alter()`

Details in `graphql_compose.api.php`

## Simple Queries

On the GraphQL Compose settings page, you can toggle simple queries for each entity type.

Make this change early in your application build, and see what works for you. Being more generic with queries could help your app grow without needing add new queries.

### Enabled simple entity type query

With simple mode enabled, you don't need to specify the bundle type in the query, and can create more generic responses through interfaces.

<!-- tabs:start -->

#### **Query**

```graphql
query {
  node(id: "38fa7cf3-d5e6-4d6c-8595-ced77e2581e2") {
    __typename
    ... on NodeInterface {
      id
      title
    }
    ... on NodeArticle {
      thumbnail {
        ... on MediaImage {
          image {
            url
          }
        }
      }
      body {
        value
      }
    }
    # ... on NodeAnotherType { ... }
  }
}
```

#### **Response**

```json
{
  "data": {
    "node": {
      "__typename": "NodeArticle",
      "id": "38fa7cf3-d5e6-4d6c-8595-ced77e2581e2",
      "title": "My Article",
      "thumbnail": {
        "image": {
          "url": "http://mysite.com/sites/default/files/2021-08/my-image.jpg"
        }
      },
      "body": {
        "value": "<p>My article body</p>"
      }
    }
  }
}
```

<!-- tabs:end -->

This could be beneficial if loading an entity by `uuid` without knowing what the `__typename` type is. By combining everything into a single query, you can make your query more flexible.

### Disabled simple entity type query

Disabling this option will split up entity queries into individual bundle queries. This means you need to know what you are querying.

<!-- tabs:start -->

#### **Query**

```graphql
query {
  nodeArticle(id: "38fa7cf3-d5e6-4d6c-8595-ced77e2581e2") {
    __typename
    id
    title
    thumbnail {
      ... on MediaImage {
        image {
          url
        }
      }
    }
    body {
      value
    }
  }
}
```

#### **Response**

```json
{
  "data": {
    "nodeArticle": {
      "__typename": "NodeArticle",
      "id": "38fa7cf3-d5e6-4d6c-8595-ced77e2581e2",
      "title": "My Article",
      "thumbnail": {
        "image": {
          "url": "http://mysite.com/sites/default/files/2021-08/my-image.jpg"
        }
      },
      "body": {
        "value": "<p>My article body</p>"
      }
    }
  }
}
```

<!-- tabs:end -->
