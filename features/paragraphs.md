# Paragraphs

- Visit GraphQL Compose &rarr; Schema at `/admin/config/graphql_compose`
- Select the `Paragraph` tab
- Select the entity bundle you want to enable.
- Click `Enable GraphQL`
- [Add some fields](core/fields.md).

## GraphQL Types

Paragraph entities are exposed to GraphQL with the interface `ParagraphInterface` and the union `ParagraphUnion`.

Each enabled paragraph type has a schema type. For example, the `wysiwyg_text` paragraph type will be typed as `ParagraphWysiwygText`.

## ParagraphInterface

```graphql
"""
Entity type paragraph.
"""
interface ParagraphInterface {
  """
  The Universally Unique IDentifier (UUID).
  """
  id: ID!

  """
  The time that the Paragraph was created.
  """
  created: DateTime!

  """
  The paragraphs entity language code.
  """
  langcode: Language!
}
```
