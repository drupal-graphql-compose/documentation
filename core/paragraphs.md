# Paragraphs

- Visit `/admin/structure/paragraphs_type`
- Next to the type you want to expose: Select `Edit`
- Down the bottom select the `GraphQL` tab
- Click `Enable GraphQL`

## GraphQL Types

Paragraph entities are exposed to GraphQL with the interface `ParagraphInterface` and the union `ParagraphUnion`.

Each paragraph type is will have a schema type. For example, the `wysiwyg_text` paragraph type will be typed as `ParagraphWysiwygText`.

> Tip: Keep your content model small, let the frontend devs do all the work.
