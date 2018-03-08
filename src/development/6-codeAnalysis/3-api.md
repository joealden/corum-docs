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

This configuration file is written in `yml`.

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
  try {
    // Create Graphcool API (based on https://github.com/graphcool/graphql-request)
    const api = fromEvent(event).api('simple/v1')

    // Retrieve payload from event
    const { data } = event
    const voteId = data.id
    const voteType = data.vote

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
      return Promise.reject('voteType is invalid or is not defined')
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

As mentioned at the top of this file, this hook function executes every time
after a `Vote` is created. It updates the `voteCount` field on the post to
reflect the vote creation. Also, if the new vote count is equal or less than the
`VOTE_COUNT_TO_DELETE_POST` constant, then the post will be deleted (The
automated management system)

As this check has to be made on every possible CRUD operation that may modify
the overall vote count, there are also hook functions for when a vote is updated
or deleted called `updateVoteCountOnVoteUpdate` and
`updateVoteCountOnVoteDeletion` respectively. These hook functions are very
similar, so I will only be analysing the 'creation' version of the hook
functions as shown above.

Variable Reference:

* `event`this is an object containing all of the information about the query or
  mutation event we are hooking into.
* `fromEvent` is a function exposed by the library `graphcool-lib` that
  constructs a GraphQL client from the `event` data passed into the function.
  This allows us to send queries and mutations to the API from within the API
  code.
* `makeRequest` is an abstraction of a function provided by `graphcool-lib` that
  allows us to send queries and mutations to the API. The exact implementation
  of this function is described later on.
* `deleteAllVotesOnPost` is a function that as the name suggests, deletes all
  votes on a post. The implementation of this function is described later on.
* `postIdFromVoteQuery` is GraphQL query that fetches the post's ID that a vote
  was made on.
* `currentPostVoteCount` is a GraphQL query that fetches the the current vote
  count of a post.
* `getAllVoteIdsOnPost` is a GraphQL query that fetches all votes that exist on
  a post.
* `deleteVote` is a GraphQL mutation that deletes a vote.
* `deletePost` is a GraphQL mutation that deletes a post.
* `updatePost` is a GraphQL mutation that updates a post with a new post count.

Like the first hook function shown in this section, this function imports what
it needs and exports a function for graphcool. Also like the first hook
function, a reference to the API is created and the user data is extracted.

This time, `voteId` and `voteType` are extracted from the data object. Next, a
request is made to get the post ID using the `voteId` variable. When the API
responds, the post ID fetched is used to fetch the current vote count on that
post.

Then, depending on how the user voted, (determined by the contents of the
`voteType` variable) A new vote count is created. If the user upvoted the post,
the vote count is increment by 1. If the user downvoted the post, the vote count
is decremented by 1. There is also error handling if they somehow submitted an
incorrect vote type.

If `voteType` was defined, the function continues and goes on to update the post
data stored in the database. This is where the automated management system
works. If the new vote count is less than or equal to the defined threshold
stored in `VOTE_COUNT_TO_DELETE_POST`, the post is deleted along with all of the
votes that were linked to it. Otherwise, the post is updated with the new vote
count.

## Resolvers

Resolvers are the most powerful way to extend graphcool. They make it so that
you can add extra queries and mutations to the API. In the case of Corum, only
two custom resolvers are needed. One to sign up users and one to authenticate
users.

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
  try {
    // Create Graphcool API (based on https://github.com/graphcool/graphql-request)
    const graphcool = fromEvent(event)
    const api = graphcool.api('simple/v1')

    // Retrieve payload from event
    const { username, email, password } = event.data

    // Check is email is valid
    if (validator.isEmail(email) === false) {
      return { error: 'The email address entered is not valid' }
    }

    // Fetch all users that match the users input (if any)
    const { allUsers } = await makeRequest(api, userQuery, { email, username })

    // If no users exists with the same details, create the user
    if (allUsers.length === 0) {
      const SALT_ROUNDS = 10

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
      return { error: 'An unknown error occurred' }
    }
  } catch (error) {
    return { error }
  }
}
```

This is the resolver that is used to create a new user account.

Variable Reference:

* `event`this is an object containing all of the information about the query or
  mutation event we are hooking into.
* `fromEvent` is a function exposed by the library `graphcool-lib` that
  constructs a GraphQL client from the `event` data passed into the function.
  This allows us to send queries and mutations to the API from within the API
  code.
* `makeRequest` is an abstraction of a function provided by `graphcool-lib` that
  allows us to send queries and mutations to the API. The exact implementation
  of this function is described later on.
* `bcryptjs` is a hashing library implementing the bcrypt hashing function
  design. In this resolver, only the `hash` function exposed by it is used. The
  use of this function will explained below.
* `validator` is a library that can validate many types of data such as emails,
  passwords, credit card numbers etc. In this resolver, only the `isEmail`
  function is used.
* `userQuery` is a GraphQL query that fetches a users details. (Their account
  ID, email and username)
* `createUserMutation` is a GraphQL mutation that creates a user.

Like the first hook function shown in this section, this function imports what
it needs and exports a function for graphcool. Also like the hook functions, a
reference to the API is created and the user data is extracted.

This time, `username`, `email` and `password` are extracted from the data
object. Next, using the `isEmail` function found in the `validator` library, the
email the user entered is checked to be valid. Internally, the `isEmail`
function is probably using regular expressions to ensure that it contains things
such as an `@` symbol etc. The `isEmail` function returns a boolean value. If it
returns `true`, the email passed to it is valid. If it returns `false`, the
email passed to it is not valid. In this case, an error is returned telling the
user that '`The email address entered is not valid`'.

Next, a request is made using the `userQuery` query passing in the email and
username the user entered. This will return an array of `User` objects that have
either the same email or username that the user inputted. If the array returned
is empty, (length equals 0) then no users exists with either the same email or
username. This means that a new account can be created with these details.

First, a variable called `SALT_ROUNDS` is initialised to the value `10`. This is
created because the bcrypt hashing function has a built in salt. This is helpful
for security reasons as it means that passwords hashed with bcrypt can easily be
protected against rainbow table attacks.

Then the hash is created from the password with the `hash` function. This
expects two arguments. The first argument being the string to hash, and the
second being the number of times to run the salt. Once the function finishes,
the function returns the hash.

Next, a request is made using the `createUserMutation` mutation passing in the
email and the username the user entered, as well as the newly created password
hash. This mutation will create a new user record with these details.

After that, we extract the user's ID from the above mutation response. Then we
generate an authentication token for the user using the `generateAuthToken` on
the `graphcool` object that was created using the `fromEvent` function earlier.
This token will allow the user perform protected actions such as creating posts
etc.

Then finally, we return the data the user wanted.

In the case that the array returned from the `userQuery` request is not empty,
this means that a user exists with some of the same data the user entered. In
order to return a useful error message to the user, a few checks are made.

These checks are pretty self explanatory, however, I will list the cases that
these checks cover:

* When both the username and password are in use, then the following could be
  possible:
  * There are 2 separate accounts, one with the same email and another with the
    same username
  * There is 1 account with the same email and the same username
* When only the email is in use, there could only be 1 account with the same
  email
* When only the username is in use, there could only be 1 account with the same
  username
* There is also an else statement at the end in case something that I haven't
  thought of occurs

Also, if any other error occurred in the function, this is caught with the
surrounding `try / catch` statement, then returned to the user.

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
    // Create Graphcool API (based on https://github.com/graphcool/graphql-request)
    const graphcool = fromEvent(event)
    const api = graphcool.api('simple/v1')

    // Retrieve payload from event
    const { email, password } = event.data

    // Check if a user exists with the email entered
    const { User } = await makeRequest(api, userQuery, { email })
    if (!User) {
      return { error: 'Invalid credentials' }
    }

    // Check if the user entered the correct password for the user
    const passwordIsCorrect = await bcryptjs.compare(password, User.password)
    if (!passwordIsCorrect) {
      return { error: 'Invalid credentials' }
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

This is the resolver that is used to authenticate users that have already
created an account. (Through the `signup` resolver)

Variable Reference:

* `event`this is an object containing all of the information about the query or
  mutation event we are hooking into.
* `fromEvent` is a function exposed by the library `graphcool-lib` that
  constructs a GraphQL client from the `event` data passed into the function.
  This allows us to send queries and mutations to the API from within the API
  code.
* `makeRequest` is an abstraction of a function provided by `graphcool-lib` that
  allows us to send queries and mutations to the API. The exact implementation
  of this function is described later on.
* `bcryptjs` is a hashing library implementing the bcrypt hashing function
  design. In this resolver, only the `compare` function exposed by it is used.
  The use of this function will explained below.
* `userQuery` is a GraphQL query that fetches a users details. (Their account
  ID, username and password)

Like the first hook function shown in this section, this function imports what
it needs and exports a function for graphcool. Also like the hook functions, a
reference to the API is created and the user data is extracted.

This time, `email` and `password` are extracted from the data object. Next, a
request is made using the `userQuery` query to fetch the users details from the
email address the user inputted. When the API responds, a check is made to
ensure that a user with the email the user typed in actually exists. If a user
doesn't exist, a generic '`Invalid Credentials`' error is thrown. This means
that the API sends back this error message to the client for it to display it to
the user.

If a user does exist with the email the user specified, then we can continue to
make further checks. Next, using the `bcryptjs.compare` function, the password
the user entered is checked against the hash that was fetched earlier from the
database. The `compare` function takes two arguments. The first argument being
the raw password that hasn't been hashed. The second argument being the password
to check against that has been hashed. Internally, the function hashes the
password and then compares it against the second argument. If they match, the
function will return `true`, if the hashes don't match, the function will return
`false`.

This comparison function is asynchronous, so we must `await` the return value.
Once the function returns the boolean value, another check is made. If the
function returned false, meaning the password the user entered wasn't correct,
then the same generic '`Invalid Credentials`' error. The reason we throw the
same error on both cases is because it makes it harder for an attacker to gain
unauthorised accessed to another users account. This is because they can't tell
if the user or password is the thing they have got incorrect.

If a user exists with both the email and password the user specified, we have a
high degree of confidence that the user is who they say they are. This means
that we can start to construct the information to return to the user. First, we
extract the user's ID and username off of the `User` object. Then we generate an
authentication token for the user using the `generateAuthToken` on the
`graphcool` object that was created using the `fromEvent` function earlier. This
token will allow the user perform protected actions such as creating posts etc.

Then finally, we return the data the user wanted. Also, if any other error
occurred in the function, this is caught with the surrounding `try / catch`
statement, then returned to the user.

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

As explained in the code comment, this is simply a wrapper around the function
`request` that is on the object returned by the `fromEvent` function. The reason
this function was created was so that I didn't have to rewrite the same error
checking functionality with every request made.

It accepts the following arguments:

* `api` is a reference to the API object created from the `fromEvent` function
* `query` is the GraphQL query or mutation to execute against the API
* `variables` is an object of variables that are used in the query or mutation

It returns a promise that is either resolved with the result, or rejected with
the error.

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

The reason for this function is best explained in the code comment above.

Variable Reference:

* `getAllVoteIdsOnPost` is a GraphQL query that gets all the `Vote` object IDs
  that are associated with the post specified.

It accepts the following arguments:

* `api` is a reference to the API object created from the `fromEvent` function
* `postId` is the post's ID to delete the votes on

It returns a promise that is resolved with the array of the deleted vote IDs.

First, a request is made using the `getAllVoteIdsOnPost` query passing in
`postId` as the queries variables. As mentioned above, this returns an array of
`Vote` objects each with an `id` property.

Then, using JavaScript built in `map` method present on the `Array` prototype,
an array is created of just the ID strings. If you do not know what a map
function does, it takes an array as input, performs an arbitrary action on each
item of the array, and then returns a new array of length. The JavaScript
implementation expects a function as input. This function will be executed on
each item of the array. To find out more about the `map` function, visit the MDN
(Mozilla Developer Network) website and search for `Array.prototype.map`.

Finally, a call to `Promise.all` is made. This function takes in an array of
promises and returns a promise. The array of promises are executed in parallel,
and the promise only resolves when all items in the promise array have resolved.
If any of the promises reject, the returned promise will reject with that
promises rejection error. To find out more about the `map` function, visit the
MDN website and search for `Promise.all`.

In this function, the array of vote IDs are mapped to an array of promises that
will make a request to delete the vote in the database with the corresponding
ID. This array is used as the argument to `Promise.all`. This means that This
function will resolve when all of the votes in the array have been deleted.
