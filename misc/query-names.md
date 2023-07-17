# Query Names

Some queries are automatically generated using a pluralisation of the entity type machine name, this can sometimes give unwanted results.

If you find the pluralisation is wrong, you can override it by creating a Drupal module and hooking `hook_graphql_compose_pluralize_alter`

Details in `graphql_compose.api.php`
