# Drupal GraphQL Compose

<a href="https://git.drupalcode.org/project/graphql_compose/-/pipelines?page=1&scope=all&ref=2.0.x">![Pipeline](https://git.drupalcode.org/project/graphql_compose/badges/2.0.x/pipeline.svg)</a>

A Drupal GraphQL 4.x schema generator.

## Install

You'll need:

- A functional Drupal `^9.5 || ^10` site.
- The [GraphQL Compose](https://www.drupal.org/project/graphql_compose) module.

### Install dependencies

```bash
composer require 'drupal/typed_data:^1.0@beta'
composer require 'drupal/graphql:^4.5'
```

### Install GraphQL Compose

```bash
composer require 'drupal/graphql_compose:^2.0'
```

### Enable the module

Install through your Drupal Admin UI or Drush.

```bash
drush pm-enable graphql_compose
```

## TL;DR

1. Enable the GraphQL Compose module.
2. Enable GraphQL Compose feature modules.
3. Enable entities and fields in your schema.

> GraphQL should be self explanatory. Use the Voyager and Explorer to make sense of the schema. This documentation is for the Drupal specific elements.
