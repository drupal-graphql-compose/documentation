# Settings

- Visit GraphQL Compose &rarr; Settings at `/admin/config/graphql_compose/settings`

## Schema information

Schema information is located within the `info` query.
You can customise what is returned by editing the `SchemaInformation` type.

```graphql
query {
  info: SchemaInformation
}

type SchemaInformation {
  """
  A custom value for schema version.
  """
  version: String

  """
  Description of the schema.
  """
  description: String
}
```

## Site information

Enable these fields to automatically pull configuration values.

- Add site name
- Add site slogan
- Add home path

```graphql
extend type SiteInformation {
  """
  The site name.
  """
  name: String

  """
  The site slogan.
  """
  slogan: String

  """
  The internal path to the front page.
  """
  home: String
}
```

## Custom settings

Custom settings can be added to extend the `info` query.

- You can create multiple values for a single field name by using the same field name.
- You can use global tokens in the Value

Each setting you define will be available within the `info` query.

> :thinking: This can work well combining tokens with modules such as [site_settings](https://www.drupal.org/project/site_settings)

## Advanced settings

### Expose entity IDs

The schema will always have UUIDs enabled. Exposing will allow loading entities by numeric id AND uuid.

For example:

<!-- tabs:start -->

#### **Query**

```graphql
query {
  a: node(id: 1) {
    ... on NodeInterface {
      title
      id
      uuid
    }
  }

  b: node(id: "7321880a-93f8-4e98-85b9-0e40fd12c3da") {
    ... on NodeInterface {
      title
      id
      uuid
    }
  }
}
```

#### **Result**

```json
{
  "data": {
    "a": {
      "title": "Home",
      "id": "1",
      "uuid": "7321880a-93f8-4e98-85b9-0e40fd12c3da"
    },
    "b": {
      "title": "Home",
      "id": "1",
      "uuid": "7321880a-93f8-4e98-85b9-0e40fd12c3da"
    }
  }
}
```

<!-- tabs:end -->

> :ghost: This could open your schema up to enumeration attack and crawling.

### Simple entity type unions

By default, the schema will use a common `<entity type>Union` for all entity references.

```graphql
type NodePage {
  relatedNode: NodeUnion
}

union NodeUnion = NodePage | NodeArticle | NodeBasicPage
```

Disabling this option will create a separate union for each entity reference field, which is limited to the available target types.

```graphql
type NodePage {
  relatedNode: NodePageRelatedNodeUnion
}

union NodePageRelatedNodeUnion = NodePage | NodeBasicPage
```
