# Subforum Page

##### Client Side

Like any other page, there are two ways to access this page. The first way is to
navigate to it via the navigation on the left of the site. The second way is for
the user to enter the url of a subforum they already know exists (For example,
`'/subforum/test'`)

The way the client knows what subforum to fetch data for is by the path variable
`:subforum` that is in the url. This value can be accessed from within the
JavaScript of the page.

This page will show the posts that have been posted in the subforum. A visual
representation of the following information can also be found in the 'UI Design'
section.

This is what a subforum page will contain:

* The title of the subforum
* A way to sort the posts (Two radio buttons):
  * Sort by most popular posts
  * Sort by newest posts
* A `New Post` button that will link to `'/subforum/:subforum/new'`
* Headers for each piece of data of a post:
  * The posts title
  * The time and date it was created at
  * The current vote count of the post
* A list containing the posts associated with the current subforum with the data
  specified by the headers above
* Each post will be a linked to `'/subforum/:subforum/post/:post'`

The `New Post` button will only be enabled (clickable) if the user is logged in,
otherwise, it will be greyed out. This is because a user should only be allowed
to create a post if they are logged in.

##### Server Side

As mentioned before, the GraphQL API exposes automatically generated queries and
mutations to perform CRUD operations. One of these exposed queries is called
`allPosts`. If I provide no parameters to the query, it will return every post
that exists on the whole site.

Two parameters that will be used to fetch the posts for a specific subforum will
be the following:

* `filter`
  * This expects a data type of 'Subforum'
  * The way I will be filtering the data is by the 'url' field on the 'Subforum'
    type.
  * This means that only the data that was posted on the subforum will be
    returned
* `orderBy`
  * This an enum called 'PostOrderBy'
  * This enum has values to sort by both descending ('DESC') and ascending
    ('ASC') of each field of 'Post'
  * The enum values that we are interested in are 'voteCount_DESC' and
    'createdAt_DESC'

The client will pass the path variable mentioned above (`:subforum`) as the
value for `filter`.

By default, the sort will be by vote count ('voteCount_DESC'). When the user
switches between the two sort types, the query will be resent to the server with
the different enum value. This means that graphcool abstracts away the sorting
functionality, meaning that I don't have to implement it myself.
