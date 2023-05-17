# Changelog

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
