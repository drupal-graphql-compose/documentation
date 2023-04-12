# Media

- Visit `/admin/structure/media`
- Next to the type you want to expose: Select `Edit`
- Down the bottom select the `GraphQL` tab
- Click `Enable GraphQL`

## GraphQL Types

Media entities are exposed to GraphQL with the interface `MediaInterface` and the union `MediaUnion`.

Each enabled media type has a schema type. For example, the `document` media type will be typed as `MediaDocument`.

> Tip: Don't forget to enable the file, remote source or image fields within your Media type.
