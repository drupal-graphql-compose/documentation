# Paragraphs

- Navigate to the `GraphQL Compose Settings` page at `/admin/config/graphql_compose`
- Select the `Paragraph` tab
- Select the entity bundle you want to enable.
- Click `Enable GraphQL`
- [Add some fields](core/fields.md).

## GraphQL Types

Paragraph entities are exposed to GraphQL with the interface `ParagraphInterface` and the union `ParagraphUnion`.

Each enabled paragraph type has a schema type. For example, the `wysiwyg_text` paragraph type will be typed as `ParagraphWysiwygText`.

> Tip: Keep your content model small, let the frontend devs do all the work.
