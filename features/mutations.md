# Mutations

> :fire: Enable the **graphql_compose_mutations** module.

This is a plug and play generic Mutation using the graphql_compose module logic. It manages CREATE, UPDATE, DELETE operations on the same Mutation.

- Most of the Drupal entity types are allowed for this mutation. They are all predefined on the enum field entity_type  of the metadata input.
- The mutation requires (at least) 2 input types. A metadata and a data object.
- The metadata objects holds the main mutation parameters such as the entity_type, entity_bundle, operation, parent_entity_type, parent_entity_id and id (if not a CREATE operation).
- The data object holds the actual values we are going to insert into or alter for the Entity.
- Since we are using this mutation in a generic way all the values are passed as an object. Each object represents a field machine name and a value.
- We can pass the uid (or user_id etc) on the data. If so the mutation will be executed for that User (if has permissions to).
- The mutation (except from the DELETE operation) returns tha basic fields from the NEW/UPDATED entity (entity_bundle, entity_type, id) as also as the entity values as a nested array. It does not return another GraphQL type so it cannot be queried instantly.
- In order to find out what fields are included (system or custom) on each Entity type and bundle you can use the Drupal module **devel**. Notice that each bundle may have different fields.
- Error violations are as much as detailed needed. No security related messages are displaying to the GraphQL endpoint users.
- DELETE operations always return the null on the `entity` field.

## GraphQL definitions

```graphql
"""
Metadata values.
"""
input genericMetadata {
  """
  The entity type (node, user, profile, group etc).
  """
  entity_type: EntityType!

  """
  The bundle of the entity. Actually, we do not need this on DELETE.
  """
  entity_bundle: String!

  """
  The operation to perform
  """
  operation: Operation!

  """
  The id of the entity (if action is not CREATE)
  """
  id: Int

  """
  The parent entity type (node, user, profile, group etc) if exists.
  """
  parent_entity_type: EntityType

  """
  The parent entity ID if exists.
  """
  parent_entity_id: Int
}

"""
The mutation available operations
"""
enum Operation {
  DEFAULT
  CREATE
  UPDATE
  DELETE
}

"""
The mutation available entity types
"""
enum EntityType {
  # More to be added here
  ACTIVITY
  COMMENT
  CONTACT_MESSAGE
  EVENT_ENROLLMENT
  FLAGGING
  GROUP
  GROUP_CONTENT
  MESSAGE
  NODE
  POST
  PRIVATE_MESSAGE
  PRIVATE_MESSAGE_THREAD
  PROFILE
  TAXONOMY_TERM
  USER
  VOTE
}

"""
A generic object containing any Drupal entity all values
"""
type AnyEntity {
  id: Int
  entity_type: String
  entity_bundle: String
  values: Json
}

type GenericEntityResponse implements Response {
  errors: [Violation]
  success: Boolean
  entity: AnyEntity
}

"""
The permissions available operations, extended
"""
enum OperationExtended {
  VIEW
  CREATE
  UPDATE
  DELETE
}

type PermissionsResponse implements Response {
  errors: [Violation]
  success: Boolean
}
```

## GraphQL available mutations and queries

```graphql
extend type Mutation {
  """
  Generic entity mutation
  """
  genericMutation(data: Json!, metadata: genericMetadata!): GenericEntityResponse
}

extend type Query {
  """
  Query to get operations by entity_type
  """
  operationsByEntityType(entity_type: EntityType!): OperationsByEntityTypeResponse
}

extend type Query {
  """
  Query to get operation permissions by uid
  """
  permissions(
    operation: OperationExtended!,
    uid: String!,
    entity_id: String,
    entity_type: EntityType!,
    entity_bundle: String!,
    parent_entity_type: EntityType,
    parent_entity_id: String,
  ): PermissionsResponse
}
```

## Mutation examples

> :thinking: Notice that some of the examples below need extra modules to be installed. Just used here as an example of how this feature works.

### genericMutation: CREATE node

```graphql
# Create a Node of bundle "event".
mutation {
  genericMutation(
    data: {
      uid: "123"
      title: "One more Event from GraphQL",
      field_content_visibility: "public",
    }
    metadata: {entity_type: NODE, entity_bundle: "event", operation: CREATE}
  ) {
    success
    errors
    entity {
      id
      entity_type
      entity_bundle
      values
    }
  }
}
```

### genericMutation: UPDATE node

```graphql
# Update a Node of bundle "event".
mutation {
  genericMutation(
    data: {
      title: "A new updated title",
      field_event_type: "10",
      field_content_visibility: "public",
      body: {
        value: "This is a new body with <p>HTML</p>",
        full_html: "full_html",
      },
      field_event_image: 44,
    }
    metadata: {
      entity_type: NODE, 
      entity_bundle: "event", 
      operation: UPDATE, 
      id: 21
    }
  ) {
    success
    errors
    entity {
        id
        entity_type
        entity_bundle
        values
    }
  }
}
```

### genericMutation: DELETE node

```graphql
mutation {
  genericMutation(
    data: {}, # data input is required, even empty
    metadata: {
      entity_type: NODE, 
      entity_bundle: "event", 
      operation: DELETE, 
      id: 21
    }) {
    success
    errors
    # This field will normally be null
    entity {
      id
    }
  }
}
```

### genericMutation: CREATE comment

```graphql
mutation {
  genericMutation(
    data: {
      # More fields can be added here
      pid: "8", # The parent comment id (if it is a reply)
      field_comment_body: "It works",
      subject: "One comment from GraphQL",
      entity_id: "40",
      entity_type:"node",
      comment_type: "comment",
      field_name: "field_comments",
    }
    metadata: {entity_type: COMMENT, entity_bundle: "comment", operation: CREATE}
  ) {
    success
    errors
    entity {
      id
      entity_type
      entity_bundle
      values
    }
  }
}
```

### genericMutation: CREATE group_content

> This mutation adds a User to a Group by creating a new **group_content** entity (see module [group](https://www.drupal.org/project/group)).
> In this case we need the `parent_entity_type` and `parent_entity_id` values due to permissions checking.

```graphql
mutation {
  genericMutation(
    data: {
      # The Group ID
      gid: "3",
      # The User ID
      entity_id: "31",
    }
    metadata: {
      entity_type: GROUP_CONTENT, 
      entity_bundle: "open_group-group_membership", 
      operation: CREATE,
      # Parent entity is required
      parent_entity_type: GROUP,
      parent_entity_id: 3
      }
  ) {
    success
    errors
    entity {
      id
      entity_type
      entity_bundle
      values
    }
  }
}
```

### genericMutation: CREATE like (vote) on node

> The vote entity is usually added on Drupal from module [votingapi](https://www.drupal.org/project/votingapi).

```graphql
mutation {
  genericMutation(
    metadata: {
      entity_type: VOTE, 
      entity_bundle: "like", 
      operation: CREATE,
      parent_entity_id: 253, # Parent entity
      parent_entity_type: NODE, # Parent entity
    }
    data: {
      # We get the Parent entity values from metadata. So this can be empty.
    }
  ) {
    success
    errors
    entity {
      id
      entity_type
      entity_bundle
    }
  }
}
```

### Query to get permissions

> Check permissions for a CUD action on entity of type group_content (module **group**).

```graphql
# Check permissions before executing the CREATE, DELETE mutation
query {
  permissions(
    operation: CREATE, # or DELETE
    uid: "25", # The User ID that will join/leave the Group
    # entity_id: "460" # The group_content ID, used on DELETE,
    entity_type: GROUP_CONTENT,
    entity_bundle: "open_group-group_membership",
    parent_entity_type: GROUP,
    parent_entity_id: "9" # The Group ID
  ) {
    errors
    success
  }
}
```

## Future plan

- Limit the allowed types from graphql_compose confuguration (not hardcoded). Maybe create a UI option.
- Check how to use the addCacheableDependency better to optimize cache.
- Validate the NEW/UPDATED entity. This needs the dedicated Entity class usage (eg the Drupal\node\Entity\Node) that we are not using at the moment due to the generic usage.
- Validate complex or composite values (e.g. entity_reference)
- Support File uploads from file URL value (this may need an additional Mutation). At the moment we need the file ID.
- Add debugging options (through a UI checkbox).
- Add testing for the Mutation API calls (get Errors, get Results etc)
