# Comments

> :fire: Enable the **graphql_compose_comments** module.

## Enabling comments

For comment to work, we need to enable the Comment type and the comment field on the entity type.

### What it does

Each comment field on a content type is converted into a Connection Edge query, and mutations are enabled to allow posting comments.

### Enable the comment type:

- Navigate to the `GraphQL Compose Settings` page at `/admin/config/graphql_compose`
- Select the `Comments` tab
- Select the comment you want to enable.
- Click `Enable GraphQL`
- Click `Enable Multiple Query`

### Enable the comment field:

Eg a Node Page, and expose that comment field within the GraphQL Compose settings form.

- Select the _type and bundle_ for the entity type you want to enable comments on (eg Node Page).
- Select the comment field
- Click `Enable GraphQL`

Now your comments are exposed as a sub-query Connection Edge.

### Example

In this example we will enable comments on the `NodeArticle` entity type with a comment type of `Node`.

<!-- tabs:start -->

#### **Query**

```graphql
{
  route(path: "/") {
    ... on RouteInternal {
      entity {
        ... on NodeArticle {
          id
          title

          comments(first: 50) {
            nodes {
              ... on CommentNode {
                id
                subject
                thread
                author {
                  name
                }
                commentBody {
                  processed
                }
              }
            }
          }
        }
      }
    }
  }
}
```

#### **Response**

```json
{
  "data": {
    "route": {
      "entity": {
        "id": "2ff9ff4d-bcc5-4c78-a39b-d32a55869756",
        "title": "Hello World",
        "comments": {
          "nodes": [
            {
              "id": "f6254907-7f86-4648-9079-f712d13d52aa",
              "subject": "Hello",
              "thread": "01/",
              "author": {
                "name": "admin"
              },
              "commentBody": {
                "processed": "<p>Testing a reply</p>\n"
              }
            }
          ]
        }
      }
    }
  }
}
```

<!-- tabs:end -->

## Writing comments

When adding a comment mutation, you need to specify what content you are adding a comment for. To get the comment type, you will use the `CommentAvailable` enum and the ID of the content you are adding a comment for.

Each _comment type_ is given it's own `Mutation`, for this example we will use a comment type with a machine name of `node` and use it's mutation called `addCommentNode`.

### Example

<!-- tabs:start -->

#### **Query**

```graphql
mutation {
  addCommentNode(
    data: {
      entityType: NodeArticle
      entityId: "2ff9ff4d-bcc5-4c78-a39b-d32a55869756"
      subject: "Hello"
      commentBody: "Millennials are killing the comment industry"
    }
  ) {
    id
    subject
    thread
    author {
      name
    }
    commentBody {
      processed
    }
  }
}
```

#### **Response**

```json
{
  "data": {
    "addCommentNode": {
      "id": "f6254907-7f86-4648-9079-f712d13d52aa",
      "subject": "Hello",
      "thread": "01/",
      "author": {
        "name": "admin"
      },
      "commentBody": {
        "processed": "<p>Millennials are killing the comment industry</p>\n"
      }
    }
  }
}
```

<!-- tabs:end -->

> Tip: Use the Explorer to find out what fields are required for your particular mutations.

> Tip: Threaded comments use some mildly confusing 01/ 02/00.0 semantics for the thread. You can use this to reconstruct the hierarchy of comments. How? No idea. But you can.
