# Breadcrumbs

> :fire: Enable the **graphql_compose_routes** module.

### `RouteInternal`

Internal routes have the breadcrumb property. [More information](features/routes.md) on how to enable loading by route.

<!-- tabs:start -->

### **Query**

```graphql
{
  route(path: "/path/to/page") {
    ... on RouteInternal {
      breadcrumbs {
        url
        title
        internal
      }
    }
  }
}
```

### **Response**

```json
{
  "data": {
    "breadcrumb": [
      {
        "url": "/path/to/page",
        "title": "Page title",
        "internal": true
      },
      {
        "url": "/path/to",
        "title": "Parent page title",
        "internal": true
      },
      {
        "url": "/path",
        "title": "Parent parent page title",
        "internal": true
      }
    ]
  }
}
```

### **Structure**

```graphql
type RouteInternal {
  breadcrumbs: [Link!]
}

type Link {
  url: String!
  title: String!
  internal: Boolean!
}
```

<!-- tabs:end -->

> :thinking: Use the _internal_ property to determine if a route is within your app or somewhere externally. It could help with transitions within your app.
