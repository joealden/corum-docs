# Client Analysis

## Nuxt configuration (`nuxt.config.js`)

```js
// For more info, visit https://nuxtjs.org/api/configuration-build

module.exports = {
  // Headers of the page
  head: {
    titleTemplate: '%s - Corum',
    meta: [
      { charset: 'utf-8' },
      { name: 'viewport', content: 'width=device-width, initial-scale=1' },
      {
        hid: 'description',
        name: 'description',
        content: 'An open, democratic & self governing forum.'
      }
    ],
    link: [{ rel: 'icon', type: 'image/png', href: '/favicon.png' }]
  },

  // CSS globals
  css: ['~/assets/styles/globals.styl'],

  // Customize the progress bar color
  // $nav-hover (Found at '~/assets/styles/variables.styl')
  loading: { color: '#53c556' },

  // Build configuration
  build: {
    // Extend the webpack config
    extend(config, { isClient, isDev }) {
      if (isClient && isDev) {
        // Run ESLint on save
        config.module.rules.push({
          enforce: 'pre',
          test: /\.(js|vue)$/,
          loader: 'eslint-loader',
          exclude: /(node_modules)/
        })
      }
    }
  },

  //  Define modules used.
  //  For more info, visit https://nuxtjs.org/guide/modules

  //  @nuxtjs/apollo - Provides vue-apollo + apollo-client
  //  @nuxtjs/dotenv - Reads .env and merges with process.env
  //  @nuxtjs/font-awesome - Provides icons
  //  @nuxtjs/pwa - Adds PWA features like offline support etc.

  //  Documentation for the modules used here can be found at:
  //  https://github.com/nuxt-community/awesome-nuxt#modules

  modules: [
    '@nuxtjs/apollo',
    '@nuxtjs/dotenv',
    '@nuxtjs/font-awesome',
    '@nuxtjs/pwa'
  ],

  // Specify the file where the apollo client config resides
  apollo: {
    clientConfigs: {
      default: '~/apollo/client-configs/default.js'
    }
  }
}
```

Please refer to the [`nuxt.config.js`](#how-nuxt-works) section located near the
top of this document for more information about what this file does and why it
exists.

These are the main section of Corum's Nuxt configuration file:

* `head`
* `css`
* `loading`
* `build`
* `modules`
* `apollo`

The following sections will describe what Corum uses them for.

### `head`

This is where page headers are defined that will be the same across all pages.

As seen below, an useful property nuxt expose is `titleTemplate`. This allows
pages to pass their names into a template, so the page title changes between
pages. Corum's title template is `'%s - Corum'`, so for example, a post page
will have its title, followed by `'- corum'`.

This also includes things such as meta tags for character encoding and site
description. Also specified here is the link to the site's favicon.

### `css`

This is where you can specify what CSS files you want to be added to the head of
every page, so these styles will be global to the entire site.

Corum's configuration references a file called `globals.styl`. It contains a
basic CSS reset, as well as globals such as the base font size and font family.

The `.styl` file extension is short for stylus. Stylus is a CSS preprocessor.
(You may have heard of SASS, which is another example of a CSS preprocessor) The
fact that I am referencing a `.styl` file is important because nuxt reads the
file extension, and applies the correct loader to process this file into regular
CSS.

### `loading`

In the GIFs files above that show how the site actually functions, you may have
noticed a small green loading bar going across the top of the page when
navigating between pages. This is a feature built into nuxt. Nuxt allows you to
customise this loading bar with CSS. Corum only changes the color of the bar to
match the overall theme.

### `build`

The build property is where Nuxt allows you to extend its functionality. The
only thing Corum uses this for is to enable linting for JavaScript and Vue
files.

### `modules`

This is where you defined the Nuxt modules that you want to use. Nuxt modules
are packages that features to Nuxt just by installing them and telling nuxt to
use them.

The modules used by Corum and why they are used can be found in the code
comments below.

### `apollo`

This is exposed by the `@nuxtjs/apollo` module, and requires you to specify
where the Apollo Client configuration file resides.

## Apollo Configuration

```js
// https://github.com/nuxt-community/apollo-module

import { ApolloLink } from 'apollo-link'
import { HttpLink } from 'apollo-link-http'
import { InMemoryCache } from 'apollo-cache-inmemory'

export default () => {
  // Create a HTTP link to the Graphcool endpoint
  const uri = process.env.API_ENDPOINT
  const httpLink = new HttpLink({ uri })

  // This middleware sends the JWT (if present) that is needed for
  // mutations such as 'createPost' or 'createComment', as only logged
  // in users are allowed to perform such mutations.

  const middlewareLink = new ApolloLink((operation, forward) => {
    /*
      Due to this being parsed SSR, process.browser must be used to
      make sure localStorage is only attempted to be accessed on the
      client. This isn't an issue as no queries or mutations that
      require authentication are made on an SSR request of the page.
    */
    const token = process.browser
      ? localStorage.getItem('auth-token')
      : undefined

    // Set the request headers to include the auth token
    operation.setContext({
      headers: { authorization: `Bearer ${token}` }
    })
    return forward(operation)
  })

  // Add this auth token middleware onto the HTTP link
  const link = middlewareLink.concat(httpLink)

  // Create an memory cache to be used by Apollo
  const cache = new InMemoryCache()

  return { link, cache }
}
```

To find out more about how configuring Apollo client works, visit the url that
is at the top of the code block.

As mentioned before, Apollo is used to communicate with Corum's GraphQL API.
Before Apollo can communicate the with the API, it needs to be configured. In
version 2 of Apollo Client, it was made a lot more modular. The two important
things to configure now are Apollo Links and Caches.

Apollo Links allow you to configure how GraphQL queries are handled. A common
Apollo link is `apollo-link-http` as seen below. This allows you to configure
how queries are sent over HTTP. The `apollo-link-http` package exposes a class
called `HttpLink`. As seen below, it expects an object containing the `uri` of
the API server. In Corum's case, the API endpoint address is fetched from the
`.env` file located in the root directory of the project. This means that
queries sent through Apollo are now sent to our Graphcool API.

There is also a generic Apollo link in the package `apollo-link`. This package
exposes a class called `ApolloLink`. This link allows you to modify the request
before it is sent in any way you like. (Also known as middleware) The reason
Corum needs to modify the request is so that the authentication token (if
present) is sent along with the request, allowing protected queries to be
performed. (Such as creating posts) The authentication token is fetched when the
user is logged in, and it is checked by the API before any protected queries are
performed, so it ensures that only logged in users can perform protected
queries.

Apollo Caches are used to cache data fetched from the server, so that unneeded
network requests are not made. How this works is that Apollo checks the cache
before following the Apollo Link chain. A type of Apollo Cache called
`apollo-cache-inmemory` is used by Corum. This simply means that the cache is
kept in memory on the client, so it is deleted when the user goes off of the
site.

## Navigation Component

### `subforumSearch`

```js
// Used to produce the filtered search
subforumSearch() {
  const caseInsensitiveInput = this.search.toLowerCase()

  return this.allSubforums.filter(subforum =>
    subforum.name.toLowerCase().includes(caseInsensitiveInput)
  )
}
```

The `subforumSearch` function is used as a computed property by Vue. Computed
properties can be used in any place where component data can be used, however
the main difference is that if any of the variables that the computed property
function is referencing change, the return value of the function will get
recomputed and the code that references this computed property will update with
this new value. To find out more about computed properties, visit the following
link: [https://vuejs.org/v2/api/#computed](https://vuejs.org/v2/api/#computed)

This function is used to calculate what items match the users search term.

As this function has been taken out of context, it will be useful to know what
the variables referenced are:

* `this.search` is a string that is linked to the search box in the navigation
  and is updated when the contents of the search box changes.
* `this.allSubforums` is an array of `Subforum` objects that has been fetched by
  Apollo. These objects contain a `name` property.

To make the search case insensitive, the `toLowerCase` method on the string
prototype is called on the users search term. Then, using the `filter` method on
the array prototype and the `includes` method on the string prototype, the
subforums that match the search term are returned from the function.

### `sortedFavorites`

```js
/*
  As graphcool doesn't support ordering by nested data, this
  computed value sorts the favorites array by subforum names.
*/
sortedFavorites() {
  if (this.allFavorites !== undefined) {
    // Spread into a new array as sort mutates original array
    return [...this.allFavorites].sort((a, b) => {
      if (a.subforum.name > b.subforum.name) {
        return 1
      } else if (a.subforum.name < b.subforum.name) {
        return -1
      } else {
        return 0
      }
    })
  } else {
    return undefined
  }
}
```

This is a Vue computed property, refer to the
[`subforumSearch`](#subforumsearch) section for more information.

Graphcool (The API framework I am using) does support ordering, (Which I have
taken advantage of when sorting posts on the subforum page) however it currently
doesn't support ordering by nested data. (As mentioned in the code comment
below) This means that instead of relying on the API to do the sorting work, I
have to perform this sorting on the client.

The reason I want the favorites to be sorted alphabetically is because
otherwise, like the `All Subforums`, it would be harder for the user to find the
subforum they are looking for when just scrolling through the list.

Variable Reference:

* `this.allFavorites` is an array of `Favorite` objects that are fetched by
  Apollo if the user is logged in. These objects contain a `Subforum` object
  with a `name` property.

Firstly, a check is made that the favorites have been fetched from the API, if
this wasn't checked, an error would be thrown. It also means that a message can
be shown to the user when the data is still loading.

If the favorites data is present, using the `sort` method on the array
prototype, a new array of `Favorite` objects is returned sorted by their
`subforum.name` property.

## Post Page

### `upvoteButtonClick`

```js
// Determines what GraphQL mutation to execute based on the vote state
upvoteButtonClick() {
  if (this.voteInProgress === false) {
    this.voteInProgress = true

    if (this.upvoted === true) {
      // The user has already upvoted
      this.localVote -= 1
      this.deleteVote({ id: this.userVoteData.id })
    } else if (this.downvoted === true) {
      // The user has already downvoted
      this.localVote += 2
      this.updateVote({ id: this.userVoteData.id, vote: 'VOTE_UP' })
    } else {
      // The user has not voted
      this.localVote += 1
      this.createVote({
        vote: 'VOTE_UP',
        postId: this.$route.params.post,
        userId: this.userId
      })
    }
  }
}
```

As shown in the 'Testing During Development' section, the post page has an
upvote and a downvote button in the top right of the page. These buttons are
bound to onClick event handlers called `upvoteButtonClick` and
`downvoteButtonClick` respectively. I am only explaining how the
`upvoteButtonClick` handler works as the `downvoteButtonClick` handler is very
similar, it just works in the opposite way. (For example, if the button is
pressed, then a downvote will be registered instead of an upvote)

Variable Reference:

* `this.voteInProgress` ensures that only a single vote is being processed at a
  time.
* `this.upvoted` keeps track of if the user has already upvoted the post
* `this.downvoted` keeps track of if the user has already downvoted the post
* `this.localVote` keeps track of the local vote count, this variable is needed
  because any data fetched from the API is immutable. If Corum relied on the
  data coming from the server only, then the UI would be slow to update, because
  an response from the API would be required to update the vote count displayed
  to the user
* `this.createVote`, `this.updateVote` and `this.deleteVote` are functions that
  send mutations requests to the API, these functions are described in the next
  code analysis sub section. This functionality is extracted out of the button
  click handler functions to reduce code duplication and keep the event handlers
  easier to read at a glance.

Firstly, a check is made to ensure that no vote mutations are in progress. If
any are in progress, then the button click is ignored. The reasoning behind this
check is explain in the variable reference section above. If the check passes,
then the vote mutation starts to execute. To ensure that the user cannot vote
again before the function has finished executing, `this.voteInProgress` is set
to `true`.

This function needed to handle the following scenarios:

* If the user hasn't voted already, increment the vote count on the post by 1
* If the user has already upvoted the post, decrement the vote count by 1
* If the user has already downvoted the post, increment the vote count by 2

This means that the function needs to handles the 3 possible branches, and
execute the correct code.

### `createVote`

```js
/*
  For more info on how mutations work within vue-apollo,
  visit https://github.com/Akryum/vue-apollo#mutations
*/

// variables = { vote, postId, userId }
async createVote(variables) {
  const postId = this.$route.params.post

  try {
    await this.$apollo.mutate({
      mutation: createVoteMutation,
      variables,

      update: (store, { data: { createVote } }) => {
        const data = store.readQuery({
          query: userVoteQuery,
          variables: {
            postId,
            userId: this.userId
          }
        })

        data.allVotes.push({
          __typename: 'Vote',
          id: createVote.id,
          vote: variables.vote
        })

        store.writeQuery({
          query: userVoteQuery,
          variables: {
            postId,
            userId: this.userId
          },
          data
        })
      },

      optimisticResponse: {
        __typename: 'Mutation',
        createVote: {
          id: 0,
          __typename: 'Vote'
        }
      }
    })

    // Indicate to the page that the vote has finished execution
    this.voteInProgress = false
  } catch (error) {
    logIfDev('error', error)
  }
}
```

Like the `upvoteButtonClick` and `downvoteButtonClick` functions, the 3 vote
mutation functions `createVote`, `updateVote` and `deleteVote` are very similar.
I will only analyse the `createVote` function.

Variable Reference:

* `variables`, as mentioned at the top of the code block, is expected to be an
  object with `vote`, `postId` and `userId` properties. These pieces of data are
  needed to correctly create the vote
* `this.$apollo` is a module exposed by the `vue-apollo` package I am using for
  Vue bindings to Apollo. This is available on the global Vue instance, this
  means that is can be accessed from any `.vue` file. It exposes as method
  called `mutate` that is used to send a mutation to the API
* `createVoteMutation` is a GraphQL mutation used to create the user
* `userVoteQuery` is a GraphQL query that returns the data for a users vote
* `this.$route.params.post` contains the postId that can be seen in the browsers
  URL bar when on a post page. Like `this.$apollo`, `this.$route` is available
  on the global Vue instance. However, instead of being exposed by `vue-apollo`,
  it is exposed by the router, `vue-router`.

The whole function is just a call to the `mutate` function found on
`this.$apollo`. The `mutate` function expects an object as an argument.

While the object argument can take more properties, these are the properties I
am using:

* `mutation` - The GraphQL mutation that you want to send to the API
* `variables` - An object of variables that the mutation takes in
* `update` - The function that is called when data is returned from the API
* `optimisticUpdate` - An object that will be used in place of the real data
  before the data is returned from the API. If this property is present, then
  the `update` function passed in will be called twice. Once immediately with
  the `optimisticUpdate` object passed as the `data` parameter of the `update`
  function, and then again whenever the data is returned from the API. This
  means that the UI can be optimistically updated before the real data from the
  API is returned. Otherwise there would be a delay between pressing the vote
  button and the UI updating, which would be a bad user experience.

To show what the `update` function does, I will explain what happens when it is
called with the `optimisticUpdate` object. As shown in the `update` function
signature, the function expects the following parameters:

* `store` - This object is a reference to the Apollo cache that exists in
  memory. (That was created by the `apollo-cache-inmemory` package as seen in
  the 'Apollo Configuration' sub section) It exposes two methods that I make use
  of called `readQuery` and `writeQuery`. As evident from their names, the
  `readQuery` function takes in a query and queries the cache with it. The
  `writeQuery` takes a query and the data to be written to the cache.
* `data` - In the case of the first call of the `update` function, this is the
  `optimisticUpdate` object.

The usual flow of the `update` function is the following:

* Read the data from the cache
* Mutation the read data
* Write the data back to the cache

This function does exactly that. The data is read, the data object is mutated by
pushing the new vote onto the end of the `allVotes` array, then the mutation
data object is written back to the cache.

Most of the function's body is wrapped in a try / catch statement. This is
needed as if any errors occur, the function will gracefully handle them. For
example, if the API is not running, then an error will be logged to the console
if the app is in development mode, using the `logIfDev` function that will be
explained later on.

### `submitComment`

```js
async submitComment() {
  const author = this.$store.state.username
  const { comment: content } = this
  const id = this.$route.params.post

  // Clears user input from textarea
  this.comment = ''

  try {
    await this.$apollo.mutate({
      mutation: createCommentMutation,
      variables: {
        author,
        content,
        id
      },

      update(store, { data: { createComment: commentData } }) {
        const data = store.readQuery({
          query: postQuery,
          variables: { id }
        })

        const newComment = {
          __typename: 'Comment',
          id: commentData.id,
          author: commentData.author,
          content: commentData.content
        }

        data.Post.comments.push(newComment)
        store.writeQuery({
          query: postQuery,
          variables: { id },
          data
        })
      },

      optimisticResponse: {
        __typename: 'Mutation',
        createComment: {
          __typename: 'Comment',
          id: 'loading',
          author,
          content
        }
      }
    })

    // scroll to bottom of page when comment is inserted
    const main = document.getElementById('main-content-wrapper')
    main.scrollTop = main.scrollHeight
  } catch (error) {
    logIfDev('error', error)
  }
}
```

Like the `upvoteButtonClick` function, this is also an event handler. This
function is called when the user clicks on the 'Post Comment' button.

Variable Reference:

* `this.$store.state.username` is a reference to the user's username.
  `this.$store` is another global Vue instance property. This is exposed by the
  `vuex` package. This allows me to store global state in Corum like user data.
* `this.comment` is a string that is linked to the comment textarea and is
  updated when the contents of the textarea changes.
* `this.$route.params.post` is a reference to the post's ID. The `this.$route`
  global is explained the 'createVote' subsection above.
* `createCommentMutation` is a GraphQL mutation that creates a comment
* `postQuery` is a GraphQL query that fetches a post's data

Just like the `createVote` function shown in the 'createVote' sub section above,
this function is mainly just a call to the `mutate` function found on
`this.$apollo`. To avoid repetition, I will not explain how this function works
again. If you don't know how it works, read the above 'createVote' sub section.

This function works in a very similar way to the `createVote` function. First,
the data required for the mutation is saved into local variables. After this,
the contents of `this.comment` is set to an empty string. This is done near the
start of the function to keep the UI feeling snappy.

Then the mutation is made. The mutation is very similar to the `createVote`
function. The update function pushes the new comment into the post's comments
array inside of Apollo's cache. After this has happened, the user is
automatically scrolled to the bottom of the page to see that their new comment
has appeared in the comments section.

Just like the `createVote` function, error handling is also present.

## New Post Page

### `submitPost`

```js
async submitPost() {
  const authorId = this.$store.state.userId

  const { postTitle: title, postContent: content, Subforum: { id } } = this
  /*
    For more info on how mutations work within vue-apollo,
    visit https://github.com/Akryum/vue-apollo#mutations
  */
  try {
    const { data: { createPost } } = await this.$apollo.mutate({
      mutation: createPostMutation,
      variables: {
        authorId,
        title,
        content,
        id
      }
    })

    const { subforum } = this.$route.params
    const postId = createPost.id
    this.$router.push(`/subforum/${subforum}/post/${postId}`)
  } catch (error) {
    this.$router.push(`/error`) // TODO: create actual error page
  }
}
```

Like the `submitComment` function, this is also an event handler. This function
is called when the user clicks on the 'Create Post' button on the 'New Post'
page. Also, instead of submitting a comment, it submits a post.

Variable Reference:

* `this.$store.state.userId` - explained in the `submitComment` sub section
* `this.postTitle` is a string that is linked to the title text box and is
  updated when the contents of the text box changes
* `this.postContent` is a string that is linked to the content textarea and is
  updated when the contents of the textarea changes
* `this.Subforum` contains the data of the subforum that the post is wanting to
  be created on
* `createPost` is a GraphQL mutation to create a new post
* `this.$route.params.subforum` is the ID of the subforum that the post is
  wanting to be created on
* `this.$router.push` is a function that redirects the user to a given url.

This function is a lot simpler than previous `mutate` function calls. This is
because when the new post is created, the user is redirected to the new post's
page, where the new post's data is fetched. This means that the cache doesn't
need to be manually updated as the redirect will trigger GraphQL queries that
will update the cache.

Unlike previous error handling code, this function redirects the user to an
error page.

I have left out a section explaining the `newSubforum` function on the 'New
Subforum' page because it is very similar, if not even more simple that this
function.

## Login Page

### `login`

```js
async login() {
  // Renders the loading submit button
  this.loading = true

  const { email, password } = this
  /*
    For more info on how mutations work within vue-apollo,
    visit https://github.com/Akryum/vue-apollo#mutations
  */
  try {
    const { data: { authenticateUser } } = await this.$apollo.mutate({
      mutation: authenticateUserMutation,
      variables: {
        email,
        password
      }
    })

    const { id, username, token } = authenticateUser
    this.$store.commit('saveUserData', { id, username, token })

    // Returns the user to the page they were on before
    // TODO: If user was on signup before, redirect to home
    this.$router.back()
  } catch ({ message }) {
    // Renders the normal submit button
    this.loading = false
    // Display the error to the user in an alert box
    alertError(message)
  }
}
```

This function is an event handler that is triggered when the users clicks on the
'Login' button on the login page. Due to the fact that the `signup` function for
the signup page is almost identical, I will not be analysing it.

Variable Reference:

* `this.loading` keeps track of if the client is currently waiting for a
  response from the API. It is used to disable the 'Login' button if it is set
  to `true` so that the user knows that page is doing something, and so that the
  user doesn't make multiple authentication requests for no reason.
* `this.email` is a string that is linked to the email text box and is updated
  when the contents of the text box changes
* `this.password` is a string that is linked to the password text box and is
  updated when the contents of the text box changes
* `authenticateUser` is a GraphQL mutation that authenticates a user
* `this.$store.commit` is a function that allows you to mutate the global state
* `this.$router.back` is a function that send the user back to the previous page
  that they were on.

This function is like many other event handlers on the site, it is basically
just a GraphQL mutation call.

If the authentication succeeds, (The user has entered the correct details) the
user's id, username and auth token is extracted from the API response and save
into the apps global state. After this, the user is redirected back to the
previous page they were on.

If the authentication fails, (The user has entered incorrect details) the
`catch` block is executed. `this.loading` is set to false so that the user can
correct the incorrect data they entered and resubmit the form. Then after this,
the error message returned from the API is cleaned up and displayed to the user
through the use of `alertError`, which will be explained later on.

## Signup Page

### `correctDetails`

```js
// Ensures that all input fields have been entered correctly
correctDetails() {
  const emailEntered = this.email !== ''
  const usernameEntered = this.username !== ''

  const password1Entered = this.password1 !== ''
  const password2Entered = this.password2 !== ''
  const passwordsEntered = password1Entered && password2Entered
  const passwordsMatch = this.password1 === this.password2

  return (
    emailEntered && usernameEntered && passwordsEntered && passwordsMatch
  )
}
```

This function is used in the signup form to perform input validation on the
client side, so that the API will have not have to deal with as much processing.

Variable Reference:

* `this.email` is a string that is linked to the email text box and is updated
  when the contents of the text box changes
* `this.username` is a string that is linked to the username text box and is
  updated when the contents of the text box changes
* `this.password1` is a string that is linked to the first password text box and
  is updated when the contents of the text box changes
* `this.password2` is a string that is linked to the second password text box
  and is updated when the contents of the text box changes

This function returns a boolean that is used in the user interface to
conditionally render a clickable signup button. If the function returns `false`,
the signup button will be greyed out. If the function returns `true`, the signup
button will be clickable.

As this is a Vue computed property, the return value will be recalculated every
time a dependency changes. (Any of the form fields)

This function works by creating a boolean from each input field. All of the
fields work in the same way, so I will only give one example.

```js
const emailEntered = this.username !== ''
```

If `this.username` is empty, `emailEntered` will evaluate to false.

The only differing piece of code in this function is where the password fields
are also checked to contain the same input. They must equal, as the user is
expected to input the exact same password.

## Utility Functions

### `stringToBoolean`

```js
// Helper function to handle process.env booleans

const stringToBoolean = stringBool => {
  if (stringBool === 'true') {
    return true
  } else if (stringBool === 'false') {
    return false
  } else {
    throw new Error(
      `Expected a string of 'true' or 'false', but got ${stringBool}`
    )
  }
}

export default stringToBoolean
```

This function is used to convert the string literals `'true'` and `'false'` into
their actual boolean values, `true` and `false`. This function is used when
importing data from the configuration file, as everything is treated as a
string.

The logic of the function is very easy to follow. If the argument to the
function is the string `'true'`, the function will return the boolean `true`. If
the argument to the function is the string `'false'`, the function will return
the boolean `false`. There is also a final check if the other conditions do not
match. For this case, a helpful error is thrown.

### `logIfDev`

```js
import stringToBoolean from '~/utils/stringToBoolean'

// Only logs to console if process.env.PROD is false

const logIfDev = (logType, message) => {
  if (stringToBoolean(process.env.PROD) === false) {
    console[logType]('test')
  }
}

export default logIfDev
```

This function conditionally logs messages to the console, depending on the
configuration option `PROD`. If the site is in development mode, the messages
passed to the function will be logged. If the site is in production mode, the
messages passed to the function will not be logged, as the end user does not
need to see these debug / error messages.

Variable Reference:

* `process.env.PROD` is the configuration option that is used to change if the
  site is in development or production mode. The `process` global is exposed by
  Nuxt, and the `env` property is exposed by the Nuxt module `@nuxtjs/dotenv`.

This functions takes a `logType` and a `message` as arguments. The `logType` can
be any valid log method on the console global, such as `log`, `warning`, or
`error`. These methods show the severity of messages logged to the console. The
`message` is expected to be a string.

### `alertError`

```js
// Formats a GraphQL error to display to the user

export const formatError = errorMessage => {
  const colonIndex = errorMessage.lastIndexOf(':')
  const cleanedMessage = errorMessage.substring(
    colonIndex + 2,
    errorMessage.length
  )
  return cleanedMessage
}

const alertError = errorMessage => {
  const cleanedMessage = formatError(errorMessage)
  alert(`Error: ${cleanedMessage}`)
}

export default alertError
```

This function is used in the signup and login pages to convert errors returned
from the API into a readable format, and then display them to the user.

There are two parts of this code, the `formatError` function and the
`alertError` function. The `formatError` actually performs the tidying up of the
error, and the `alertError` calls the `formatError` function and displays it.

The reason this function is needed is because the error message returned from
the API is for debugging, and contains data that is not useful to the end user.

## Global Store Configuration (Vuex)

```js
import { Store } from 'vuex'
import Vue from 'vue'

// For more info, visit https://nuxtjs.org/guide/vuex-store/

/*
  Vue.set() is used to get around Vue's inability to detect the
  state change.
*/

export default () => {
  return new Store({
    // Only allow state mutations through the defined mutation methods
    strict: true,

    /*
      Initial state is fetched in a hook that executes on the client.
      Fetch can be found at '~/layouts/default' or '~/layouts/error'.
    */
    state: {
      userId: undefined,
      username: undefined
    },

    mutations: {
      logout(state) {
        localStorage.removeItem('user-id')
        localStorage.removeItem('username')
        localStorage.removeItem('auth-token')
        Vue.set(state, 'userId', localStorage.getItem('user-id'))
        Vue.set(state, 'username', localStorage.getItem('username'))
      },

      saveUserData(state, { id, username, token }) {
        localStorage.setItem('user-id', id)
        localStorage.setItem('username', username)
        localStorage.setItem('auth-token', token)
        Vue.set(state, 'userId', localStorage.getItem('user-id'))
        Vue.set(state, 'username', localStorage.getItem('username'))
      },

      updateUserState(state) {
        Vue.set(state, 'userId', localStorage.getItem('user-id'))
        Vue.set(state, 'username', localStorage.getItem('username'))
      }
    }
  })
}
```

This file is used to configure the state management library that I am using,
`Vuex`. The reason Corum needs a global state management library is because the
user details need to be available across the entire app. Instead of calling out
to the `localStorage` API every time Corum needs the user information, it is
quicker to keep this information in memory.

Nuxt expects this file to export a function that returns the store to use in the
application. the `Store` class is exposed by the `Vuex` library, and expect a
configuration object as a parameter. This object can contain many properties,
but I will only explain the ones that I am using.

* `strict` - As explained in the comment in the code block, if set to `true`,
  this prevents the state from being changed directly without using the methods
  defined in the `mutations` property.
* `state` - This is an object that is used to initialise the state. Corum only
  uses two state properties, `userId` and `username`.
* `mutations` - This is a object of functions that can be called to mutate the
  `state` object, as well as perform other operations, such as setting or
  removing data from the browsers `localStorage`.

There are three mutation methods in Corum's store. `logout`, `saveUserData` and
`updateUserState`. `logout` removes saved data from the browsers `localStorage`
as well as from the store. This mutation is called when the `logout` button is
pressed in the sites header. `saveUserData` updates the browsers `localStorage`
and the store with the data passed in. This mutation is called when logging the
user into the site, or after a user signs up (As it also logs them in).
`updateUserState` updates the state by fetching the data from the browser's
`localStorage`. This is needed as `localStorage` persists in the browser across
visits, whereas Corum's global state cannot persist. Upon every visit, this
mutation is called. This means that a user can be kept logged into Corum across
visits.
