# Users

> :fire: Enable the **graphql_compose_users** module.

- Visit GraphQL Compose &rarr; Schema at `/admin/config/graphql_compose`
- Select the `User` tab
- Click `Enable GraphQL`
- [Add some fields](core/fields.md).

> :thinking: If you want to load users by route, ensure you have the correct permissions in place.

<!-- tabs:start -->

### **Example Query**

User has the option to `Enable Single Query`.

```graphql
# Specific user
user(id: "43267052-d965-11ed-afa1-0242ac120009") {
  name
}

# Current user
viewer {
  name
}
```

### **Result**

```json
{
  "data": {
    "user": {
      "name": "Bob"
    },
    "viewer": {
      "name": "Admin"
    }
  }
}
```

### **UsersInterface**

```graphql
"""
Entity type user.
"""
interface UserInterface {
  """
  The Universally Unique IDentifier (UUID).
  """
  id: ID!

  """
  The time that the user was last edited.
  """
  changed: DateTime!

  """
  The time that the user was created.
  """
  created: DateTime!

  """
  The email of this user.
  """
  mail: Email

  """
  The name of this user.
  """
  name: String!

  """
  The roles the user has.
  """
  roles: [UserRoles!]

  """
  Whether the user is active or blocked.
  """
  status: UserStatus!
}
```

<!-- tabs:end -->

> :thinking: Emails are protected. Ensure you have the correct user permissions in place if you want to expose this data.

## Node author information

Once enabled, `NodeInterface` will receive the `author: User` field.

This will populate only if the node bundle is configured to expose the author.

To expose author information:

- Visit the `Structure` &rarr; `Content Types` &rarr; `Edit` tab on your node bundle
- Enable `Display author and date information`.
