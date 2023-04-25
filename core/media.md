# Media

- Navigate to the `GraphQL Compose Settings` page at `/admin/config/graphql_compose`
- Select the `Media` tab
- Select the entity bundle you want to enable.
- Click `Enable GraphQL`
- [Add some fields](core/fields.md).

## GraphQL Types

Media entities are exposed to GraphQL with the interface `MediaInterface` and the union `MediaUnion`.

Each enabled media type has a schema type. For example, the `document` media type will be typed as `MediaDocument`.

> Tip: Don't forget to enable the file, remote source or image fields within your Media type.
