# Getting started

## Install the module

You'll need:

- A functional Drupal 10 site.
- The [GraphQL](https://www.drupal.org/project/graphql) module.
- The [GraphQL Compose](https://www.drupal.org/project/graphql_compose) module.

### Install with composer

```bash
composer require 'drupal/graphql_compose:^2.0@beta'
```

## Enable it

For an overwhelmingly long explanation, head to the Drupal.org docs: https://www.drupal.org/docs/extending-drupal/installing-modules

- Enable GraphQL
- Enable GraphQL Compose
- Enable GraphQL Compose feature modules.

## Create a schema

- Go to `/admin/config/graphql` and click `Create server`
- Give your server a label
- Select the `GraphQL Compose Schema`
- Give your server an endpoint URL, `/graphql` works fine.
- Save

> Note: IGNORE any **Schema configuration** fields when creating your server, leave them all unchecked. Thats a form render bug with GraphQL module.
