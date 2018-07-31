# Building Scalable APIs with GraphQL

## GraphQL Query Language

* queries can have names

### Fields

* Types

  * Scalar
    * GraphQLInt
    * GraphQLFloat
    * GraphQLString
    * GraphQLBoolean
    * GraphQLID
      * unique identifier to refresh object data
  * Complex

  * Wrapping types
    * GraphQLList
    * GraphQLNonNull

* Resolver functions
  * Determine the field value

### Variables

* Needs a scope and a type

```grapql
query TestQuery($currentUserName: String!) {
  github {
    user(username: $currentUserName) {
      id
      company
      avatar_url
      repos {
        name
      }
    }
  }
}
```

`$currentUserName` is a **variable**. It needs to have a scope defined. In this case, the _scope_ is `TestQuery`. It also have a type: `String`, which must be the same type of the _corresponding field_. The `!` indicates that this variable is _required_.

Variables are provided (typically) in a **JSON** object field called _variables_.

### Directives

Directives are user to alter the GraphQL runtime execution

* @ skip

```graphql
...
field_name @skip(if: $condition)
...
```

* @include

```graphql
...
field_name @include(if: $condition)
...
```

### Aliases

* To map data names to the ui names

```
...
githubid: id
...
```

* Differentiate fields in union queries

```
...
user1: user(username: $user1) {
  id
  name
  email
}
user2: user(username: $user2) {
  id
  name
  email
  avatar_url
}
...
```

### Fragments

* It's a partial operation
* Used for composition
* `...`: _spread operator_

```
...
user1: user(username: $user1) {
  ...UserInfo
}
user2: user(username: $user2) {
  ...UserInfo
  avatar_url
}
...

fragment UserInfo on GithubUser {
  id
  name
  email
}
```

### Inline Fragments

* For querying fields of _multiple types_
* Interfaces & Unions

```
# Interface

        Person
     ______|________
     |             |
     |             |
  Employee       Vendor
```

```
# Union

Github User       Guest User
  |___________________|
            |
            |
          Author
```

```
...
commits {
  message
  author {
    ... on GithubUser {
      login
    }
    ... on GithubCommitAuthor {
      email
    }
  }
}
...
```

### Mutations

* Update data
* Side effects

```
mutation setName {
  setName(name: "Zuck") {
    newName
  }
}

mutation AddResource($input: CreateLinkInput!) {
  createLink(input: $input) {
    linkEdge {
      node {
        id
      }
    }
  }
}
```

Every mutation has _Input_ and _Output_

* Input: What the server uses to apply the mutation
* Output: Everything that can be read after mutation execute
