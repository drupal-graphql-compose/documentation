# Taxonomy Terms

- Visit GraphQL Compose &rarr; Schema at `/admin/config/graphql_compose`
- Select the `Taxonomy term` tab
- Select the entity bundle you want to enable.
- Click `Enable GraphQL`
- [Add some fields](core/fields.md).

<!-- tabs:start -->

### **Example Query**

Each term vocabulary has the option to `Enable Single Query`.

The term's schema type name is used to name the query. For example, an `image` node type will have a query of `termImage`.

```graphql
termTag(id: "43267052-d965-11ed-afa1-0242ac120004") {
  id
  name
  parent {
    name
  }
}
```

### **Result**

```json
{
  "data": {
    "termTag": {
      "id": "43267052-d965-11ed-afa1-0242ac120004",
      "name": "My Tag",
      "parent": {
        "name": "My Parent Tag"
      }
    }
  }
}
```

### **TermInterface**

```graphql
"""
Entity type taxonomy_term.
"""
interface TermInterface {
  """
  The Universally Unique IDentifier (UUID).
  """
  id: ID!

  """
  The time that the term was last edited.
  """
  changed: DateTime!

  """
  Description
  """
  description: Text!

  """
  The term language code.
  """
  langcode: Language!

  """
  Name
  """
  name: String!

  """
  The parents of this term.
  """
  parent: TermUnion

  """
  URL alias
  """
  path: String!

  """
  Published
  """
  status: Boolean!
}
```

<!-- tabs:end -->

## GraphQL Types

Taxonomy Term entities are exposed to GraphQL with the interface `TermInterface` and the union `TermUnion`.

Each enabled vocabulary type has a schema type. For example, the `tags` vocabulary type will be typed as `TermTags`.

> :thinking: Don't name your content type "Union" or "Interface"
