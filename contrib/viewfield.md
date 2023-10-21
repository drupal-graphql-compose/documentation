# Viewfield (contrib)

We support the ability to add a view as a field on a type. To enable, install the [Viewfield](https://www.drupal.org/project/viewfield) module.

If enabled, an option to embed your view will be available on the GraphQL Compose Schema configuration page.

> You should be mindful of any performance implications of enabling views as fields. Can a _bad actor_ request a ton of data on your website all at once? _Maybe_ they can via menus or edge connections.

## Install Viewfield module

```bash
composer require 'drupal/viewfield:^3.0@beta'
```

## Add a viewfield field to your entity (eg Node Page)

- Visit `/admin/structure/types`
- Next to the type you want to expose: Select `Manage Fields`
- Click `Add field`
- Select `Reference` &rarr; `Viewfield`
- Add label and continue

Select your GraphQL enabled views in the Field Settings page. Eg:

- View: `Cool Beans`
- Allowed display types: `GraphQL`

Now go and edit your content, add the view field and select the view display you want to use.

## Add a view field to your GraphQL schema

Navigate to the `GraphQL Compose Settings` page at `/admin/config/graphql_compose` and enable your field.

## Querying an embedded field

On your GraphQL Compose Settings page, for your new viewfield, select the option to `Embed view as field`. This will allow you to add arguments to the view and filter the results.

We create an intermediate _proxy_ field for getting your view data which will return a `ViewResultUnion`.

This proxy allows for queries, with slightly altered input fields to make it more generic:

- `sortKey` is now a `String`
- `pageSize` is now controlled by the viewfield
- `filter` is an array of `{key: '', value: ''}`
- `contextualFilter` is now controlled by the viewfield

As each field could return a new view type, it's difficult to map without a proxy.

> :thinking: Viewfield contextual filters can use tokens! So you could get the current entity ID and pass it to the viewfield as a contextual filter.

### Example query

<!-- tabs:start -->

#### **Query**

```graphql
query {
  node(id: "38fa7cf3-d5e6-4d6c-8595-ced77e2581e2") {
    ... on NodePage {
      myViewField(sortKey: "TITLE", filter: [{ "key": "sticky", "value": "1" }]) {
        ... on MyViewResult {
          pageInfo {
            page
            pageSize
            total
          }
          results {
            ... on NodePage {
              id
              sticky
            }
          }
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
    "node": {
      "myViewField": {
        "pageInfo": {
          "page": 0,
          "pageSize": 10,
          "total": 1
        },
        "results": [
          {
            "id": "38fa7cf3-d5e6-4d6c-8595-ced77e2581e2",
            "sticky": true
          }
        ]
      }
    }
  }
}
```

<!-- tabs:end -->
