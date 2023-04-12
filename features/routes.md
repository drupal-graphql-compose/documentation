# Routes

> :fire: Enable the **graphql_compose_routes** module.

- Visit `/admin/structure/types`
- Next to the type you want to expose: Select `Edit`
- Down the bottom select the `GraphQL` tab
- Click `Enable loading by route`

## Basic usage

Route enables the `route(path: String!)` query which enables you to load an entity by it's routed URL.

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

<!-- tabs:end -->

## Anatomy of a Route

Each enabled entity is added into the `RouteEntityUnion` union. If you wish to resolve your entity with a Route, you will need to enable loading by route on that entity.

### `RouteInternal`

Represents a path that resolves to an internal entity and has an `entity` `RouteEntityUnion` property which

### `RouteExternal`

Generally this isn't returned from the `route` query, but could be returned from a `route` property on a `Link` type.

### `RouteRedirect`

Represents a path that resolves to a redirect. Route redirects could be redirecting internally or externally, and have a status code.

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
