# Revisions & Previews

Node previews in Drupal are loaded by UUID.

## Load a preview by route

```graphql
{
  route("/node/{preview_uuid}/preview") {
    ... on RouteInternal {
      entity {
        ... on NodeArticle {
          title
        }
      }
    }
  }
}
```

## Load a revision

```graphql
{
  route("/node/{uuid}/revision/{revision_id}") {
    ... on RouteInternal {
      entity {
        ... on NodeArticle {
          title
        }
      }
    }
  }
}
```
