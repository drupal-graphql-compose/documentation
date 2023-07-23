# Changelog

## 2.0.0 - Beta 9 (dev)

- Add `Layout Builder` support.
- Resolve #3374255 Unsupported union.
- Extend #3366581 Add hooks to hardcoded unions
- Add preview and revision support for route()
- Add `MetaTagInterface` to metatag enabled entities.
- Add metatag to all available entities, rather than just node.
- Add `hook_graphql_compose_entity_interfaces_alter`

## 2.0.0 - Beta 8

`drush updatedb` required.

### General

- Drupal 9.5 support
- Add example Vue3 app to documentation.
- Add settings panel.
- Add ability to toggle on entity IDs.
- Add configurable schema version/description.
- Add configurable site details (name, slogan, home).
- Add Gitlab CI & some unit tests.
- Add Drupal config schema.
- General PHPCS cleanup.
- Resolve #3366088 "Entity reference fields", Add toggle.
- Removal of general interface `Node`. **BREAKING CHANGE?**

### Blocks

- Change `Block` to `BlockInterface` **BREAKING CHANGE**
- Change query arg block_plugin_id: String! to id: ID! **BREAKING CHANGE**

### Edge

- Enforce published in `Edge` query
- Change `Node` in `Edge` to `EdgeNode` **POSSIBLE BREAKING CHANGE**
- Add langcode support to edges.

### Terms

- Issue #3364017: Expose parent term id for Taxonomy Term schema

### Routes

- Issue #3364408 by Yury N, almunnings: Routes: language argument is not passed to RouteEntity producer

## 2.0.0 - Beta 7

- Oopsies, Just some PHPCS fixes.

## 2.0.0 - Beta 6

### MenuItem

Removal of route resolution from MenuItem to avoid excessive data loading by bad actors.

- Add `MenuItem.id`
- Add `MenuItem.url`
- Add `MenuItem.internal`
- Convert to optional, `MenuItem.route`
- Remove `Link.route`
- Add `Link.url`
- Add `Link.internal`

If upgrading shift your requirements away from `MenuItem.route`

### Viewfield support

Add [Viewfield](https://www.drupal.org/project/viewfield) support.

- Enables embedding of views within fields.
