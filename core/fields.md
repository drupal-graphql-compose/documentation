# Add Fields

To add a field to the schema, in your field config UI:

- Navigate to the `GraphQL Compose Settings` page at `/admin/config/graphql_compose`.
- Select the entity type that has your field.
- Select the entity bundle that has your field.
- Scroll down to your field.
- Click `Enable GraphQL`

## Field name

The default field name will be a camel case version of the field's machine name, with "field" removed.

- Field: `field_my_field`
- Schema name: `myField`

Optionally rename your field in the schema by editing the field and giving it any name you want.

> Renaming can be useful for avoiding naming convention conficts and avoid the need for making aliases within your queries.
