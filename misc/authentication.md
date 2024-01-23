# Authentication

> GraphQL compose doesn't handle your authentication, but heres some tips.

## Example: Simple OAuth

Using [Simple Oauth](https://www.drupal.org/project/simple_oauth) 5x we can use the "password" grant type to authenticate a user. Whilst not the _most_ secure, it's a good starting point.

```bash
composer require drupal/simple_oauth:^5
drush pm-install simple_oauth
```

Simple Oauth 6x doesn't support password grants, but theres _many ways to skin a cat_. :cat:

### TL;DR

Use OAuth to authorize your GraphQL requests.

### Configure

- Create a key directory somewhere in your project, outside of drupal's web/docroot. _How you manage these keys is up to you. I suggest not putting them in your git repo though, generate new keys per server_.
- Visit the Simple Oauth configuration page: `/admin/config/people/simple_oauth`
- Click `Generate Keys`
- Enter in the relative path to your keys dir from your project. Eg `../keys`
- `/admin/config/services/consumer`
- `Add Consumer`
- Add secret, something like `testing123``

### An example using vanilla javascript

#### Make a password request to your consumer.

```javascript
const getToken = () =>
  fetch('https://your.backend.io/oauth/token', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/x-www-form-urlencoded',
    },
    body: new URLSearchParams({
      username: 'test-user',
      password: 'test-user',
      grant_type: 'password',
      client_id: 'default',
      client_secret: 'testing123',
    }),
  }).then((response) => response.json());
```

#### Make a query to GraphQL with your token.

```javascript
const getQuery = (token, query) =>
  fetch('https://your.backend.io/graphql', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      Authorization: `Bearer ${token}`,
    },
    body: JSON.stringify({ query }),
  }).then((response) => response.json());
```

#### Put it all together, and query some the current user.

```javascript
const getExample = async () => {
  const token = await getToken();
  const result = await getQuery(
    token.access_token,
    'query { viewer { name, mail } }'
  );

  console.log(result);
};

getExample();
```

```json
{
  "data": {
    "viewer": {
      "name": "test-user",
      "mail": "test-user@test-user.com"
    }
  }
}
```
