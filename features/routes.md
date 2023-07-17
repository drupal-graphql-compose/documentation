# Routes

> :fire: Enable the **graphql_compose_routes** module.

## Enabling routes

After enabling the routes module, Each entity type has the option to `Enable loading by route`.
It also adds enables the `route(path: String!)` query, allowing you to load an entity by it's routed URL.

<!-- tabs:start -->

### **Query**

```graphql
{
  route(path: "/home") {
    ... on RouteInternal {
      entity {
        ... on NodePage {
          id
          title
        }
      }
    }
  }
}
```

### **Response**

```json
{
  "data": {
    "route": {
      "entity": {
        "id": "2ff9ff4d-bcc5-4c78-a39b-d32a55869756",
        "title": "Home"
      }
    }
  }
}
```

### **Structure**

```graphql
type Query {
  route(path: String!, langcode: String): RouteUnion
}

union RouteUnion = RouteInternal | RouteExternal | RouteRedirect

interface Route {
  url: String!
  internal: Boolean!
}

type RouteInternal implements Route {
  url: String!
  internal: Boolean!
  breadcrumbs: [Link!]
  entity: RouteEntityUnion
}

union RouteEntityUnion = NodeArticle | NodePage | # Anything else enabled

type Link {
  url: String!
  title: String!
  internal: Boolean!
}

type RouteExternal implements Route {
  url: String!
  internal: Boolean!
}

type RouteRedirect implements Route {
  url: String!
  internal: Boolean!
  status: Int!
  redirect: Boolean!
}
```

<!-- tabs:end -->

## Anatomy of a Route

Each enabled entity is added into the `RouteEntityUnion` union. If you wish to resolve your entity with a Route, you will need to enable loading by route on that entity.

### `RouteInternal`

Resolves to an internal type. Use the `entity` property to get deeper information on the content. Internal routes also have [breadcrumbs](features/breadcrumbs.md).

### `RouteExternal`

Generally this isn't returned from the route _query_, but could be returned from a route _field_ on a `Link` type.

### `RouteRedirect`

Requires the [Redirect](https://www.drupal.org/project/redirect) module. Represents a path that resolves to a redirect. Route redirects could be redirecting internally or externally, and have a status code.

## Language support

If your site has languages enabled, you can also use the `langcode` property on the route query.

<!-- tabs:start -->

### **Query**

```graphql
{
  english_home: route(path: "/home") {
    ... on RouteInternal {
      entity {
        ... on NodePage {
          title
        }
      }
    }
  }
  japanese_home: route(path: "/home", langcode: "ja") {
    ... on RouteInternal {
      entity {
        ... on NodePage {
          title
        }
      }
    }
  }
}
```

### **Response**

```json
{
  "data": {
    "english_home": {
      "entity": {
        "title": "Home"
      }
    },
    "japanese_home": {
      "entity": {
        "title": "ホムペ"
      }
    }
  }
}
```

<!-- tabs:end -->

> Tip: Use the _internal_ property to determine if a route is within your app or somewhere externally. It could help with transitions within your app.
