# Concepts

## What have we done

We've built a GraphQL schema that exposes the data from Drupal in a way that makes sense for a decoupled application. We're trying to keep it simple, we're trying to let you create lean schemas. We're trying to make it easy to use.

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

We took a Drupal approach to building the schema, load only what you want via the Plugin API. It also enables you as a developer to extend and add your own fields, entities and features.
