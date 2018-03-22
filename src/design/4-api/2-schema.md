# API Schema (Data Structures)

In GraphQL, similar to SQL databases, the API has a schema. This schema is
defined in the GraphQL SDL. (Schema Definition Language) This allows me to
declaratively define the structure of my data.

As quoted from graphcool's website:

> The main components of a Schema Definition are the types and their fields.

For example, the `Post` type will have a field such a `title`, that will be of
type `String`. Also:

* Field types such as `String` can be followed by an `!` (`String!`) to show
  that a field is required
* Arrays are specified by surrounding a type with `[]`. For example, an array of
  strings would be specified by `[String]`

> Additional information can be provided as custom directives.

For example, I could:

* Specify default values for a field using the `@default` directive
* Specify that a field must be unique to all other data of the same type using
  the `@isUnique` directive
* Tell graphcool to generated the CRUD API for this type using the `@model`
  directive
* Tell graphcool that there is a relation between two fields (like relations in
  SQL) with the `@relation` directive
  * These can be `1-1`, `1-n`, or `n-n` relations
  * In my data, I will only be needing `1-1` and `1-n` mappings

To find out more information about GraphQL schemas, visit
[graph.cool](https://www.graph.cool/docs/reference/database/data-modelling-eiroozae8u/)
and search the documentation for the section on 'Data Modelling'.

## The Schema for Corum

```graphql
type User @model {
  id: ID! @isUnique
  username: String! @isUnique
  email: String! @isUnique
  password: String!
  posts: [Post!]! @relation(name: "UserToPost")
  votes: [Vote!]! @relation(name: "UserToVote")
  favorites: [Favorite!]! @relation(name: "UserToFavorite")
}

type Subforum @model {
  id: ID! @isUnique
  url: String! @isUnique
  name: String!
  posts: [Post!]! @relation(name: "SubforumToPost")
  favorites: [Favorite!]! @relation(name: "SubforumToFavorite")
}

type Favorite @model {
  id: ID! @isUnique
  user: User! @relation(name: "UserToFavorite")
  subforum: Subforum! @relation(name: "SubforumToFavorite")
}

type Post @model {
  id: ID! @isUnique
  subforum: Subforum! @relation(name: "SubforumToPost")
  title: String!
  author: User! @relation(name: "UserToPost")
  content: String!
  voteCount: Int
  createdAt: DateTime!
  comments: [Comment!]! @relation(name: "PostToComment")
  votes: [Vote!]! @relation(name: "PostToVote")
}

type Comment @model {
  id: ID! @isUnique
  post: Post! @relation(name: "PostToComment")
  author: String!
  content: String!
}

type Vote @model {
  id: ID! @isUnique
  post: Post! @relation(name: "PostToVote")
  user: User! @relation(name: "UserToVote")
  vote: VoteType!
}

enum VoteType {
  VOTE_UP
  VOTE_DOWN
}
```

Here is a graphical representation of the schema above:

![Graph View of API Schema](images/api-schema-graph-view.png)
