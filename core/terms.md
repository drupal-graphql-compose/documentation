# Taxonomy Terms

- Visit `/admin/structure/taxonomy`
- Next to the type you want to expose: Select `Edit vocabulary`
- Down the bottom select the `GraphQL` tab
- Click `Enable GraphQL`

## GraphQL Types

Taxonomy Term entities are exposed to GraphQL with the interface `TermInterface` and the union `TermUnion`.

Each vocabulary type is will have a schema type. For example, the `tags` vocabulary type will be typed as `TermTags`.
