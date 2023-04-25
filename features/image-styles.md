# Image Styles

> :fire: Enable the **graphql_compose_image_style** module.

- Navigate to the `GraphQL Compose Settings` page at `/admin/config/graphql_compose`
- Select the `Image style` tab
- Select the entity bundle you want to enable.
- Click `Enable GraphQL`

## What it is

Enabling image styles adds the `variations(styles: [ImageStyleAvailable])` sub-query to `Image` types. This enables you to get the URLs and dimensions of the image in different styles.

Each enabled image style is added into the `ImageStyleAvailable` enum. For example, an image style called `large_2x` would become the enum property `LARGE2X`.

Enum's can be combined in the `variations` query to get the desired content.

## Example

<!-- tabs:start -->

#### **Query**

```graphql
{
  #...
  image {
    alt
    title
    variations(styles: [LARGE, MEDIUM]) {
      name
      url
      width
      height
    }
  }
  #...
}
```

#### **Response**

```json
{
  "data": {
    "image": {
      "alt": "A cool image",
      "title": "Cool Image",
      "variations": [
        {
          "name": "LARGE",
          "url": "http://localhost/sites/default/files/styles/large/public/2021-01/cool_image.jpg?itok=Z8Z8Z8Z8",
          "width": 1000,
          "height": 1000
        },
        {
          "name": "MEDIUM",
          "url": "http://localhost/sites/default/files/styles/medium/public/2021-01/cool_image.jpg?itok=Z8Z8Z8Z8",
          "width": 500,
          "height": 500
        }
      ]
    }
  }
}
```

<!-- tabs:end -->
