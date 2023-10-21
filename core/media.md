# Media

- Visit GraphQL Compose &rarr; Schema at `/admin/config/graphql_compose`
- Select the `Media` tab
- Select the entity bundle you want to enable.
- Click `Enable GraphQL`
- [Add some fields](core/fields.md).

<!-- tabs:start -->

### **Example Query**

Each media type has the option to `Enable Single Query`.

The media's schema type name is used to name the query. For example, an `image` node type will have a query of `mediaImage`.

```graphql
media(id: "43267052-d965-11ed-afa1-0242ac120003") {
  ... on MediaInterface {
    id
    name
  }
  ... on MediaImage {
    image {
      alt
      title
      url
    }
  }
}
```

### **Result**

```json
{
  "data": {
    "media": {
      "id": "43267052-d965-11ed-afa1-0242ac120003",
      "name": "My Image",
      "image": {
        "alt": "My Image",
        "title": "My Image",
        "url": "http://mysite.com/sites/default/files/2021-08/my-image.jpg"
      }
    }
  }
}
```

### **MediaInterface**

```graphql
"""
Entity type media.
"""
interface MediaInterface {
  """
  The Universally Unique IDentifier (UUID).
  """
  id: ID!

  """
  The time the media item was last edited.
  """
  changed: DateTime!

  """
  The time the media item was created.
  """
  created: DateTime!

  """
  Language
  """
  langcode: Language!

  """
  Name
  """
  name: String!

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

Media entities are exposed to GraphQL with the interface `MediaInterface` and the union `MediaUnion`.

Each enabled media type has a schema type. For example, the `document` media type will be typed as `MediaDocument`.

> :thinking: Don't forget to enable the file, remote source or image fields within your Media type.
