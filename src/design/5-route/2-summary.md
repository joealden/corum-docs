# Route Summary

Here is a brief summary of what all of the different routes will do.

* [`'/'`](#sub-forum-not-selected-logged-in-ui) - Displays a user guide that
  explains how the site works and how to do things.
* [`'/login'`](#login-page-ui) - Displays a login screen (Username + password)
* [`'/signup'`](#sign-up-page-ui) - Displays a sign up screen (Username +
  password + password)
* [`'/subforum/:subforum'`](#sub-forum-selected-not-logged-in-ui) - Displays the
  posts from the selected sub-forum. (Using the `:subforum` variable)
* [`'/subforum/:subforum/post/:post'`](#post-view-logged-in-ui) - Displays the
  post selected. (Using the `:post` variable)
* [`'/subforum/:subforum/new'`](#new-post-ui) - Displays the new post entry
  fields.

The following sections will describe each of these routes in more detail. Most
of the following sections will contains subsections about the design of the
client and the design of the server/backend (API). If they do not have these
subsections it will be because all of the information is about the client, as no
server side processing is required.
