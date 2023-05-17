# UUID

## ID is a weird string

It's not weird, it's useful! `ID` fields will return the `UUID` of an entity. The UUID is a unique identifier for the entity. You can use that UUID to key your components or do specific customisations in your app.

## How to get something's UUID?

You would come across the UUID of an entity as a result of using another query...

> We've included a "Copy UUID" link next to nodes when you're logged in as an admin. This will copy the UUID to your clipboard.

In a pinch you can use drush.

For example: \
Getting the UUID of a Node:

```bash
drush php:eval "echo \\Drupal\\node\\Entity\\Node::load(1)->uuid->value . PHP_EOL;"
```
