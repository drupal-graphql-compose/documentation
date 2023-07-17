# Menus

> :fire: Enable the **graphql_compose_menus** module.

- Navigate to the `GraphQL Compose Settings` page at `/admin/config/graphql_compose`
- Select the `Menu` tab
- Select the menu you want to enable.
- Click `Enable GraphQL`

## What it is

Enabling menus adds the `menu(name: MenuAvailable!)` query.

Each enabled menu is added into the `MenuAvailable` enum. For example, an menu called `top_right` would become the enum property `TOPRIGHT`.

## Example

<!-- tabs:start -->

### **Query**

```graphql
{
  menu(name: MAIN) {
    name
    items {
      title
      description
      url
    }
  }
}
```

### **Response**

```json
{
  "data": {
    "menu": {
      "name": "Main navigation",
      "items": [
        {
          "title": "Home",
          "description": "Sample home link",
          "url": "/home"
        },
        {
          "title": "Another page",
          "description": "This is a description",
          "url": "/another-page"
        }
      ]
    }
  }
}
```

### **Schema**

```graphql
"""
Entity type menu.
"""
interface MenuInterface {
  """
  The Universally Unique IDentifier (UUID).
  """
  id: ID!

  """
  The menu items.
  """
  items: [MenuItem!]!

  """
  The menu name.
  """
  name: String!
}

"""
Entity type menu.
"""
type Menu implements MenuInterface {
  """
  The Universally Unique IDentifier (UUID).
  """
  id: ID!

  """
  The menu items.
  """
  items: [MenuItem!]!

  """
  The menu name.
  """
  name: String!
}

"""
A menu item defined in the CMS.
"""
type MenuItem {
  """
  The Universally Unique IDentifier (UUID).
  """
  id: ID!

  """
  The title of the menu item.
  """
  title: String!

  """
  The description of the menu item.
  """
  description: String

  """
  The URL of the menu item.
  """
  url: String

  """
  Whether this menu item links to an internal route.
  """
  internal: Boolean!

  """
  Whether this menu item is intended to be expanded.
  """
  expanded: Boolean!

  """
  Attributes of this menu item.
  """
  attributes: MenuItemAttributes!

  """
  Child menu items of this menu item.
  """
  children: [MenuItem!]!

  """
  The route this menu item uses. Route loading can be disabled per menu type.
  """
  route: RouteUnion
}
```

<!-- tabs:end -->

## Menu children

There is a recurring limitation in GraphQL. To get menus to _n levels deep_ you have to define each level. To simplify the query you can use a fragment on the `children` property.

```graphql
fragment MenuItemFragment on MenuItem {
  title
  description
  url
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
