# API Analysis

## API Schema

The schema of the API was created during the design phase of the project.
However, so it is easier to understand the code in this section, I will repeat
it here:

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

## Permission Configuration

```yml
# Where 'authenticated: true' is present in an operation,
# the client must pass along their JWT auth token in the
# request headers. (In the form -> Authorization: 'Bearer ${TOKEN}')

permissions:
    # User Permissions

    # Allows access to User from Post
    # Only the username field is made queryable. This means
    # that malicious users cannot query for someones elses
    # information such as their password, id, or email.
  - operation: User.read  
    fields: [username]

  # Subforum Permissions
  - operation: Subforum.create
    authenticated: true
  - operation: Subforum.read

  # Favorite Permissions
  - operation: Favorite.create
    authenticated: true
  - operation: Favorite.read
  - operation: Favorite.delete
    authenticated: true

  # Post Permissions
  - operation: Post.create
    authenticated: true
  - operation: Post.read

  # Comment Permissions
  - operation: Comment.create
    authenticated: true
  - operation: Comment.read

  # Vote Permissions
  - operation: Vote.create
    authenticated: true
  - operation: Vote.read
  - operation: Vote.update
    authenticated: true
  - operation: Vote.delete
    authenticated: true

  # Relation Permissions
  - operation: SubforumToPost.connect
    authenticated: true
  - operation: PostToComment.connect
    authenticated: true
  - operation: UserToPost.connect
    authenticated: true
  - operation: UserToVote.connect
    authenticated: true
  - operation: PostToVote.connect
    authenticated: true
  - operation: UserToFavorite.connect
    authenticated: true
  - operation: SubforumToFavorite.connect
    authenticated: true
```

As mentioned in the '[How Graphcool Works](#how-graphcool-works)' section,
graphcool needs a configuration file called `graphcool.yml`. Most of this file
is just boilerplate to link the below hook functions into graphcool, however a
very important part of this file is the `permissions` section.

This section allows you to define exactly who can do what with the API. This is
crucial to the proper functioning of Corum as it stops non authenticated users
from doing things like creating posts, creating comments and voting on posts.

This configuration file is written in `yml`, which you may not be that familiar
with. Here are the basics that are put to use in this file:

* `#` starts a comment
* `-` starts an object

So in this file, `permissions` is a list of objects. Each object in the
`permissions` array specifies the `operation` to configure. The operations that
can be configured are derived from the API schema. (Linked above)

For example, The `Vote` data type found in the schema can have the following
operations configured:

* `Vote.create`
* `Vote.read`
* `Vote.update`
* `Vote.delete`

These operations are found on every data type in the schema. (Which are CRUD
operations) You can also specify permissions for relations. This is important
because creating a relationship between data types means that both data type
instances are mutated. For example, the following operations can be specified
for the `UserToPost` relation which links a user to the post they created:

* `UserToPost.connect`
* `UserToPost.Disconnect`

If a certain operation is not specified, (for example, there is not
`Favorite.update` operation in this configuration) then nothing (expect hook
functions given root tokens) can perform this operation.

If an object in the `permissions` section specifies an `authenticated` property
of `true`, the operation can only be performed by authenticated users (Users
that are logged in)

If an object in the `permission` section specifies a `field` property, then
operation is restricted to only the values found in the array passed to it. For
example, in Corum's API, anyone can read from the `Username` data type, however
they can only read the `username` field.

## Hook Functions

### `userAndSubforumIsUnique`

```js
import { fromEvent } from 'graphcool-lib'
import { makeRequest } from '../../utils/common'

/*
  This is a hook function that executes every time before a favorite is created.
  It ensures that only 1 favorite of a subforum by a single user can happen.
*/

const favoriteQuery = `
query FavoriteQuery($userId: ID!, $subforumId: ID!) {
  allFavorites(
    filter: { AND: [{ user: { id: $userId } }, { subforum: { id: $subforumId } }] }
  ) {
    id
  }
}
`

export default async event => {
  try {
    // Create Graphcool API (based on https://github.com/graphcool/graphql-request)
    const api = fromEvent(event).api('simple/v1')

    // Retrieve payload from event
    const { data } = event
    const { userId, subforumId } = data

    const { allFavorites } = await makeRequest(api, favoriteQuery, {
      userId,
      subforumId
    })

    if (allFavorites.length === 0) {
      return { data }
    } else {
      return { error: 'This user has already favorited this subforum' }
    }
  } catch (error) {
    return { error }
  }
}
```

As mentioned in the comment at the top of this file, This hook function ensures
that only 1 favorite of a subforum by a single user can happen, and it is run
every time before a favorite is created. There is also a hook function called
`userAndPostIsUnique` is very similar. Instead of ensuring that only unique
favorites can be added, it ensures that only unique votes can be made on a post.
As this hook function is so similar, I will only analyse this hook function.

Variable Reference:

* `favoriteQuery` is the GraphQL query used to check if the user has already
  favorited of this subforum.
* `event`this is an object containing all of the information about the query or
  mutation event we are hooking into.
* `fromEvent` is a function exposed by the library `graphcool-lib` that
  constructs a GraphQL client from the `event` data passed into the function.
  This allows us to send queries and mutations to the API from within the API
  code.
* `makeRequest` is an abstraction of a function provided by `graphcool-lib` that
  allows us to send queries and mutations to the API. The exact implementation
  of this function is described later on.

First, `fromEvent` and `makeRequest` are imported so that they can be used in
this file. Next, we export a function that expects the `event` argument. The
reason we do this is because graphcool expects hook function files to export a
function that it can call when needed.

Within this function, using the `fromEvent` function, a reference to the API is
created from the `event` data and assigned to the variable `api`. This variable
will be passed into the `makeRequest` function so that it knows what API to make
the request to.

Next, the data the client submitted to the API is extracted from the `data`
property of the `event` argument. For this hook function, we extract `userId`
and `subforumId`. Then, a call to the `makeRequest` function is made, passing in
the API reference created earlier, the GraphQL query that we want to execute,
along with the variable needed to perform the query. In this case, both the
`userId` and `subforumId` are passed as variables to the query.

Once the query has successfully been executed, the function will return the data
response. In this case, we are only interested in the `allFavorites` value that
is returned. This is an array of `Favorite` objects that belong to the user and
match the subforum they are attempting to create a favorite for. This means that
if the array has any `Favorite` objects in it, the user has already favorited
this subforum.

This check is made by checking if the length of the array is 0. (empty) If the
array is empty, then the user has not favorited this subforum already, so they
should allow to favorite it. In this case, the data submitted by the user is
saved to the database. If the length of the array is anything other than 0, then
user should not be allowed to create a favorite. An error is returned to the
user and the data is not saved.

### `initVoteCount`

```js
// Ensures that when a post is created, the vote Count is set to 0

export default event => {
  const { data } = event
  data.voteCount = 0
  return { data }
}
```

This is the simplest hook function used in Corum. As mentioned in the code
comment, when a `Post` is created, the `voteCount` field on it is set to 0. This
is so that malicious users cannot initialise a post with however many votes they
want.

### `updateVoteCountOnVoteCreation`

```js
import { fromEvent } from 'graphcool-lib'
import {
  VOTE_COUNT_TO_DELETE_POST,
  makeRequest,
  deleteAllVotesOnPost,
  postIdFromVoteQuery,
  currentPostVoteCount,
  getAllVoteIdsOnPost,
  deleteVote,
  deletePost,
  updatePost
} from '../../utils/common'

/*
  This is a hook function that executes every time after a vote is created.
  It updates the voteCount field on the post to reflect the vote creation.
  Also, if the new vote count is equal or less than the VOTE_COUNT_TO_DELETE_POST
  constant, then the post will be deleted (The automated management system)
*/

export default async event => {
  // Retrieve payload from event
  const { data } = event
  const voteId = data.id
  const voteType = data.vote

  // Create Graphcool API (based on https://github.com/graphcool/graphql-request)
  const graphcool = fromEvent(event)
  const api = graphcool.api('simple/v1')

  try {
    const { Vote } = await makeRequest(api, postIdFromVoteQuery, { voteId })
    const postId = Vote.post.id

    const { Post } = await makeRequest(api, currentPostVoteCount, { postId })
    const oldVoteCount = Post.voteCount

    let newVoteCount
    if (voteType === 'VOTE_UP') {
      newVoteCount = oldVoteCount + 1
    } else if (voteType === 'VOTE_DOWN') {
      newVoteCount = oldVoteCount - 1
    } else {
      return Promise.reject('voteType is not defined')
    }

    if (newVoteCount <= VOTE_COUNT_TO_DELETE_POST) {
      await deleteAllVotesOnPost(api, postId)
      return await makeRequest(api, deletePost, { postId })
    } else {
      return await makeRequest(api, updatePost, { postId, newVoteCount })
    }
  } catch (error) {
    return { error }
  }
}
```

* placeholder
* mention how there are also the other hook functions for delete and update

## Resolvers

### `authenticate`

```js
import { fromEvent } from 'graphcool-lib'
import * as bcryptjs from 'bcryptjs'
import { makeRequest } from '../../utils/common'

const userQuery = `
  query UserQuery($email: String!) {
    User(email: $email) {
      id
      password
      username
    }
  }
`

export default async event => {
  try {
    // Retrieve payload from event
    const { email, password } = event.data

    // Create Graphcool API (based on https://github.com/graphcool/graphql-request)
    const graphcool = fromEvent(event)
    const api = graphcool.api('simple/v1')

    // Check if a user exists with the email entered
    const { User } = await makeRequest(api, userQuery, { email })
    if (!User) {
      return { error: 'Invalid credentials!' }
    }

    // Check if the user entered the correct password for the user
    const passwordIsCorrect = await bcryptjs.compare(password, User.password)
    if (!passwordIsCorrect) {
      return { error: 'Invalid credentials!' }
    }

    // Generate auth token
    const { id, username } = User
    const token = await graphcool.generateAuthToken(id, 'User')

    // Return the payload the user asked for
    return {
      data: {
        id,
        username,
        token
      }
    }
  } catch (error) {
    return { error }
  }
}
```

placeholder

### `signup`

```js
import { fromEvent } from 'graphcool-lib'
import * as bcryptjs from 'bcryptjs'
import * as validator from 'validator'
import { makeRequest } from '../../utils/common'

const userQuery = `
  query UserQuery($email: String!, $username: String!) {
    allUsers(filter: { OR: [{ email: $email }, { username: $username }] }) {
      id
      password
      email
      username
    }
  }
`

const createUserMutation = `
  mutation CreateUserMutation(
    $username: String!
    $email: String!
    $passwordHash: String!
  ) {
    createUser(username: $username, email: $email, password: $passwordHash) {
      id
    }
  }
`

export default async event => {
  // Retrieve payload from event
  const { username, email, password } = event.data

  // Create Graphcool API (based on https://github.com/graphcool/graphql-request)
  const graphcool = fromEvent(event)
  const api = graphcool.api('simple/v1')

  const SALT_ROUNDS = 10

  try {
    // Check is email is valid
    if (validator.isEmail(email) === false) {
      return { error: 'The email address entered is not valid' }
    }

    // Fetch all users that match the users input (if any)
    const { allUsers } = await makeRequest(api, userQuery, { email, username })

    // If no users exists with the same details, create the user
    if (allUsers.length === 0) {
      // Generate the password hash that will be stored in the DB
      const passwordHash = await bcryptjs.hash(password, SALT_ROUNDS)

      // Create the user
      const { createUser } = await makeRequest(api, createUserMutation, {
        email,
        username,
        passwordHash
      })

      // Generate auth token
      const { id } = createUser
      const token = await graphcool.generateAuthToken(id, 'User')

      // Return the payload the user asked for
      return {
        data: {
          id,
          token
        }
      }
    }

    // If any users do exist, throw the correct informative error
    if (
      allUsers.length === 2 ||
      (allUsers[0].email === email && allUsers[0].username === username)
    ) {
      return { error: 'The email address and username are in use' }
    } else if (allUsers[0].email === email) {
      return { error: 'The email address is in use' }
    } else if (allUsers[0].username === username) {
      return { error: 'The username is in use' }
    } else {
      return { error: 'An unknown error occured' }
    }
  } catch (error) {
    return { error }
  }
}
```

placeholder

## Utility Functions and Constants

### `VOTE_COUNT_TO_DELETE_POST`

```js
export const VOTE_COUNT_TO_DELETE_POST = -1
```

This number determines the amount of votes required for the post to be deleted
by the automatic post management system.

### `makeRequest`

```js
/*
  A wrapper around the fromEvent(event).request that is provided
  by 'graphcool-lib' to handle errors in query responses.
*/
export const makeRequest = async (api, query, variables) => {
  const queryResult = await api.request(query, variables)

  if (queryResult.error) {
    return Promise.reject(queryResult.error)
  } else {
    return queryResult
  }
}
```

placeholder

### `deleteAllVotesOnPost`

```js
/*
  Currently, the CRUD API generated by graphcool does not expose a
  query that allows you to update or delete multiple data records at
  once.

  It looks like this feature is coming in the graphcool 1.0 release.
  https://github.com/graphcool/framework/issues/81

  In the meantime, this function will be used to delete all the Votes
  on a Post record. This is required because graphcool doesn't allow
  you to delete records that would leave other records orphans.

  In the case of the Post type, if it was deleted, it would leave the
  Vote records that reference the Post, orphans. To allow us to delete
  a post, we must first delete the Votes that reference it.
*/
export const deleteAllVotesOnPost = async (api, postId) => {
  const { allVotes } = await makeRequest(api, getAllVoteIdsOnPost, { postId })
  const voteIdList = allVotes.map(vote => vote.id)

  return await Promise.all(
    voteIdList.map(voteId => {
      return makeRequest(api, deleteVote, { voteId })
    })
  )
}
```

placeholder
