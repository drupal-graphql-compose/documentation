# Entity Construction Kit (contrib)

> :fire: Enable the **graphql_compose_eck** module.

## Install ECK module

```bash
composer require 'drupal/eck:^2.0'
```

## What is ECK?

More on [Entity Construction Kit](https://www.drupal.org/project/eck).

> The Entity Construction Kit (ECK) builds upon the entity system to create a flexible and extensible data modeling system both with a UI for site builders, and with useful abstractions (classes, plugins, etc) to help developers use entities with ease.

Basically it's a dynamic entity type builder. Pretty useful.

- Enable the `eck` module and create some entity types.

After setting up your ECK modules.

- Visit GraphQL Compose &rarr; Schema at `/admin/config/graphql_compose`
- Select the tab that matches your ECK entity type
- Select the entity bundle you want to enable.
- Click `Enable GraphQL`
- [Add some fields](core/fields.md).

<!-- tabs:start -->

## GraphQL Types

ECK entities are exposed to GraphQL with a prefix of the machine name of your ECK entity.

EG ECK type: "My Bananas" with a machine id of `my_bananas` will create `MyBananasInterface` and the union `MyBananasUnion`.

Each enabled eck type bundle has a schema type. For example, the `bunch` _my_bananas_ type will be typed as `MyBananasBunch`.

> :thinking: Don't name your ECK bundles "Union" or "Interface"

## ECK Entity interfaces

Base fields on ECK entities are configurable, check your GraphQL Explorer for available fields.
