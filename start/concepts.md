# Concepts

## Its empty!

Yes! By default the module will expose nothing. It's "a choose your own adventure". You should be:

- Enabling the features you want
- Enabling entity types
- Enabling fields on entity types

And we'll do the rest.

If something isn't appearing in your schema, check the following:

- The entity type is supported
- The entity type is enabled
- The field is supported
- The field is enabled

## Why feature submodules?

We took a Drupal approach to building the schema, load only what you want via the Plugin API.

## ID is a weird string

It's not weird, it's useful! `ID` fields will return the `UUID` of an entity. The UUID is a unique identifier for the entity. You can use that UUID to key your components or do specific customisations in your app.

### How to get something's UUID?

You would come across the UUID of an entity as a result of using another query.... In a pinch you can use drush.

For example: \
Getting the UUID of a Node:

```bash
drush php:eval "echo \\Drupal\\node\\Entity\\Node::load(1)->uuid->value . PHP_EOL;"
```
