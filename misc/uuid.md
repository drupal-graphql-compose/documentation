# UUID

## ID is a weird string

It's not weird, it's useful! `ID` fields will return the `UUID` of an entity. The UUID is a unique identifier for the entity. You can use that UUID to key your components or do specific customisations in your app.

## I'd rather not use UUIDs

There is a setting to enable numeric IDs on the GraphQL Compose settings page. This will enable loading by ID and UUID simultaneously. You can then use the `id` and `uuid` field to get the regular ID/UUID where applicable.

## How to get something's UUID?

You would come across the UUID of an entity as a result of using another query.

We've included a "Copy UUID" link next to nodes when you're logged in as an admin. This will copy the UUID to your clipboard.
