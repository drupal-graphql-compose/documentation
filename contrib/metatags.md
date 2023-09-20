# Metatag (contrib)

> :fire: Enable the **graphql_compose_metatags** module.

## Install Metatag module

```bash
composer require 'drupal/metatag:^2.0'
```

Entities that can display metatags will receive a `metatag` field and the `MetaTagInterface` interface.

If using Metatag v1.x please see issue [2945817](https://www.drupal.org/project/metatag/issues/2945817) for a required patch.

<!-- tabs:start -->

### **Query**

```graphql
{
  route(path: "/") {
    ... on RouteInternal {
      entity {
        ... on MetaTagInterface {
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
        "metatag": [
          {
            "tag": "link",
            "attributes": {
              "rel": "canonical",
              "href": "https://my-website.com.site/article/123"
            }
          },
          {
            "tag": "meta",
            "attributes": {
              "name": "title",
              "content": "News article one two three | Demo"
            }
          }
        ]
      }
    }
  }
}
```

<!-- tabs:end -->
