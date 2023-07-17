# Views

> :fire: Enable the **graphql_compose_views** module.

## Create the base view

- Add a new view `/admin/structure/views/add`
- Give your view a label
- Leave Page and Block blank
- `Save and edit`

On the next page:

- Click `+ Add` to add a display
- Select `GraphQL`

## View settings

It's mostly just views settings. You can add filters, arguments, relationships, etc.

Give your query a query name. for example: `myQueryNameHere`
From there the following enums are possible:

- `MyQueryNameHereSortKeys` - Sorts available to the query
- `MyQueryNameHereFilterInput` - Filters available to the query
- `MyQueryNameHereContextualFilterInput` - Contextual filters available to the query

And the result type will be `MyQueryNameHereResult`, with an interface of `View`

### Pagination

Items to display: Enable the full or mini pager (it doesn't matter) in the view to enable the `page` argument.

Under the pager _options_, these settings are available:

- `pageSize`: Allow user to control the number of items displayed in this view
- `offset`: Allow user to specify number of items skipped from beginning of this view.

## Sample query

> Tip: _Currently_ there is no real context for the view, if using contextual filters, weigh up how they are actually different to contextual filters. Consider using a viewfield for context.

<!-- tabs:start -->

### **Query**

```graphql
{
  coolBeans(
    sortKey: TITLE
    sortDir: ASC
    page: 0
    pageSize: 3
    offset: 0
    filter: { sticky: true }
  ) {
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
```

### **Response**

```json
{
  "data": {
    "coolBeans": {
      "pageInfo": {
        "page": 0,
        "pageSize": 3,
        "total": 1
      },
      "results": [
        {
          "id": "b2ce44b4-d58d-4847-909f-491a7b677032",
          "sticky": true
        }
      ]
    }
  }
}
```

<!-- tabs:end -->

> Tip: Your query structure will vary to the above examples based on what you have enabled within your view. Check your GraphQL Explorer for the available options under your query and input types.

## Field Views

Under `Format` &rarr; `Show`, change _Entity_ to _Fields_

The Settings allow you to choose what your data will be:

- String
- Int
- Float
- Boolean
- Custom Scalar

Most of the time it's going to be a String, but you can choose to have raw data. Add your fields to the view and update the settings to match your data.

> Tip: If you choose to have raw data, using a Custom Scalar can help avoid Type issues. It's a weak type, but it gets the job done.
