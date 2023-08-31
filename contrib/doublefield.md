# Double Field (contrib)

> GraphQL Compose supports the [Double Field](https://www.drupal.org/project/double_field) module.

## Install Double Field module

```bash
composer require 'drupal/double_field:^4.1'
```

## Schema types

Each double field you expose becomes a combination type of the two field types selected, prefixed with `Double`.

Example:

- First field: A plain text field
- Second field: A date field

Results in

```graphql
type DoubleStringDateTime {
  first: String
  second: DateTime
}
```
