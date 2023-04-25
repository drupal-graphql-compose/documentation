# Taxonomy Terms

- Navigate to the `GraphQL Compose Settings` page at `/admin/config/graphql_compose`
- Select the `Taxonomy term` tab
- Select the entity bundle you want to enable.
- Click `Enable GraphQL`
- [Add some fields](core/fields.md).

## GraphQL Types

Taxonomy Term entities are exposed to GraphQL with the interface `TermInterface` and the union `TermUnion`.

Each enabled vocabulary type has a schema type. For example, the `tags` vocabulary type will be typed as `TermTags`.
