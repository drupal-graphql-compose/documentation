# Metatags

> :fire: Enable the **graphql_compose_metatags** module.

## Install Metatag module

```bash
composer require 'drupal/metatag:^2.0@RC'
```

## Add a metatag field to your entity (eg Node Page)

- Visit `/admin/structure/types`
- Next to the type you want to expose: Select `Manage Fields`
- Click `Add field`
- Select `General` &rarr; `Meta Tags`
- Add label and continue
- Save
- Navigate to the `GraphQL Compose Settings` page at `/admin/config/graphql_compose` and enable your field.

<!-- tabs:start -->

### **Query**

```graphql
{
  route(path: "/") {
    ... on RouteInternal {
      entity {
        ... on NodePage {
          id
          metatag {
            ... on MetaTag {
              tag
            }
            ... on MetaTagLink {
              attributes {
                rel
                href
              }
            }
            ... on MetaTagValue {
              attributes {
                name
                content
              }
            }
            ... on MetaTagProperty {
              attributes {
                property
                content
              }
            }
          }
        }
      }
    }
  }
}
```

### **Response**

```json
{
  "data": {
    "route": {
      "entity": {
        "id": "2ff9ff4d-bcc5-4c78-a39b-d32a55869756",
        "metatag": [
          {
            "tag": "link",
            "attributes": {
              "rel": "canonical",
              "href": "https://my-website.com.site/home"
            }
          },
          {
            "tag": "meta",
            "attributes": {
              "name": "title",
              "content": "Home | Demo"
            }
          }
        ]
      }
    }
  }
}
```

<!-- tabs:end -->
