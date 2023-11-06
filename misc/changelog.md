# Changelog

## 2.0.0 - You go girl

Major feature/refactor wise, we good to go into semantic versioning. \
I'd prefer to get some new features in now with a 2.1.0 release.

Upgrading from beta to 2.0.0 docs: https://drupal-graphql-compose.github.io/documentation/#/misc/upgrading

- Introduce simple entity type query for load by id (Advanced settings)
- Add entity type singuliarizing (Advanced settings)
- Add Exclude unpublished option (Advanced settings)
- Add JSON field module support
- Add publication_date module support
- Issue #3390718: Add support for office hours module
- Issue #3386690: Undefined method LinkItem::getTitle() in version 2.0.0-beta11
- Issue #3388138: Taxonomy Term cache tags appear not to be propagating
- Issue #3390590: MenuItem route should not inherit language from menu
- Issue #3390437: Sub-directory installation support for routes
- Issue #3388494: Enabling metatags submodule generates error "Interface field MetaTagInterface.metatag expected
- Issue #3389528: Leaked breadcrumb metadata
- Issue #3388045: Translations of referenced entities.
- Issue #3399402: Allow usage of langcode as an option, on nodetypes
- Move entity reference entity loads into producer with buffer (performance)
- General changes: Cache checking and entity translation
- Breaking: Remove AttributesType and use UntypedStructuredData
- Breaking: GraphQLComposeFieldType interface changes
- Breaking: K, lets get out of beta. :tada:

## 2.0.0 - Beta 11

`drush updatedb` required.

- Feature: Support `svg_image` module.
- Feature: Support `eck` entity.
- Feature: Support veeerrryyy basic `group` entity.
- Feature: Setting for connection edge max limits.
- Add `size` to `Image` for image file size.
- Fix issue #3383868 `TermInterface` `weight` field.
- Fix views field max-age 0.
- Move preview entity loading to preview buffer.
- Refactor to enable derivers for EntityType plugins.
- Deprecate `graphql_compose_fragments` into main module.
- Remove `token` module dependency.
- API Change: `NodeInterface.author: User!` is now `User` to allow for controlled access.

## 2.0.0 - Beta 10

Few cache related fixes. Bit boring.

- Fix cache leak on file/image
- Fix breadcrumbs forcing cache max-age 0
- Fix preview cache should have max-age 0
- Fix revision by route, actually do the thing.
- Add breadcrumb test, _it now works_.

## 2.0.0 - Beta 9

Major feature for this release it experimental Layout Builder.
This is a work in progress and will be improved in future releases.

- Add experimental `Layout Builder` support.
- Add preview and revision url support to `route()`.
- Add metatag to all applicable entity types.
- Add `MetaTagInterface` to metatag enabled entities.
- Add `hook_graphql_compose_entity_interfaces_alter`.
- #3366581 Add hooks to hardcoded unions.
- #3376780 Add `Geofield` support.
- #3376730 Add `Doublefield` support.
- #3361379 Add support for custom setting exposure.
- #3374255 Resolve unsupported union of unsupported.
- Change `graphql_compose_fragments` and `graphql_compose_layout` to hidden modules.
- Remove `false|null` field/entity config on schema settings save.
- Documentation is now _moody_. :sunglasses:

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
