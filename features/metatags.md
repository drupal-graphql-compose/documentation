# Metatags

> Enable the **graphql_compose_metatags** module.

## Patch required

You'll require a patch in your composer json to use metatags due to a compatibility issue https://www.drupal.org/project/metatag/issues/2945817.

The latest known good patch at time of writing is:
https://www.drupal.org/files/issues/2022-10-13/metatag-data-type-support-2945817-154.patch

```json
  "patches": {
      "drupal/metatag": {
          "2945817 Metatag DataType": "https://www.drupal.org/files/issues/2022-10-13/metatag-data-type-support-2945817-154.patch"
      }
  }
```

## Install MetaTag module

```bash
composer require 'drupal/metatag:^1.22'
```

## Add a metatag field to your entity (eg Node Page)

- Visit `/admin/structure/types`
- Next to the type you want to expose: Select `Manage Fields`
- Click `Add field`
- Select `General` &rarr; `Meta Tags`, label it and save.

## Sample Query

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

## Sample result

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