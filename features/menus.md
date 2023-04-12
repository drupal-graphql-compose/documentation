# Menus

> :fire: Enable the **graphql_compose_menus** module.

- Visit `/admin/structure/menu`
- Next to the menu you want to expose: Select `Edit menu`
- Down the bottom select the `GraphQL` fieldset
- Click `Enable GraphQL`

## What it is

Enabling menus adds the `menu(name: MenuAvailable!)` query.

Each enabled menu is added into the `MenuAvailable` enum. For example, an menu called `top_right` would become the enum property `TOPRIGHT`.

## Example

<!-- tabs:start -->

#### **Query**

```graphql
{
  menu(name: MAIN) {
    name
    items {
      title
      description
      route {
        ... on Route {
          url
        }
      }
    }
  }
}
```

#### **Response**

```json
{
  "data": {
    "menu": {
      "name": "Main navigation",
      "items": [
        {
          "title": "Home",
          "description": "Sample home link",
          "route": {
            "url": "/home"
          }
        },
        {
          "title": "Another page",
          "description": "This is a description",
          "route": {
            "url": "/another-page"
          }
        }
      ]
    }
  }
}
```

<!-- tabs:end -->

## Menu children

There is a recurring limitation in GraphQL. To get menus to _n levels deep_ you have to define each level. To simplify the query you can use a fragment on the `children` property.

```graphql
fragment MenuItemFragment on MenuItem {
  title
  description
  route {
    ... on Route {
      url
    }
  }
}

{
  menu(name: MAIN) {
    items {
      ...MenuItemFragment
      children {
        ...MenuItemFragment
        children {
          ...MenuItemFragment
        }
      }
    }
  }
}
```

> Tip: Your GraphQL server has no idea what page is _active_. Manage active state within your own app.
