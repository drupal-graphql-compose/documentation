# Views

> :fire: Enable the **graphql_compose_views** module.

You can expose your enabled GraphQL Compose entities via a GraphQL Compose view.

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

## Pagination

Items to display: Enable the full pager (mini works, but won't have a total count) in the view to enable the `page` argument.

Under the pager _options_, these settings are available:

- `pageSize`: Allow user to control the number of items displayed in this view
- `offset`: Allow user to specify number of items skipped from beginning of this view.

## Filtering

To enable filters on your view, just add a filter to your view, and expose it to the user.

EG Adding the sticky filter.

- Click `Filter criteria` &rarr; `add`
- Search and add `Sticky at top of lists` in the `Content` category
- Enable `Expose this filter to visitors`
- Apply and save

> :collision: If your view returns an entity type that is not supported and enabled by GraphQL Compose, it's going to throw an error. Filter your views content types accordingly. Eg if you have a view that returns `Node` entities, you should limit the content types to your enabled bundles.

## Field Views

Optionally you can expose individual fields rather than entities. To do so

- In the Format section, click `Show` &rarr; `Entity` and change to `Fields`

Now add your fields to the `Fields` section.

Most of the time results are going to be a String, but you can choose to have raw data.

- In the Format section, click `Show` &rarr; `Settings` to modify the result field data types.

Options are:

- String
- Int
- Float
- Boolean
- Custom Scalar

> :thinking: If you choose to have raw data, using a Custom Scalar can help avoid Type issues. It's a weak type, but it gets the job done.

## Sample query

<!-- tabs:start -->

### **Query**

```graphql
{
  myTestView(
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
    "myTestView": {
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

> :thinking: Your query structure will vary to the above examples based on what you have enabled within your view. Check your GraphQL Explorer for the available options under your query and input types.

> :thinking: _Currently_ there is no real context for the view. If using contextual filters, weigh up how they are actually different to _regular_ filters. Consider using a viewfield for context.
