# API Architecture

## Graphcool

* A way to quickly create a production ready GraphQL server
* Acts almost like a database, as it provides CRUD operation on the data
* The way this works is that it generates a CRUD API with GraphQL queries and
  mutations based on the schema you give it

### Why Graphcool?

* Kind of like how Nuxt is the Vue, Graphcool is to a GraphQL server
* Means I do not have to reinvent the wheel.

### How Graphcool Works

* CLI (Command Line Interface)
  * `gcf` (Stands from Graphcool Framework)
  * `gcf deploy` - Deploys the API to a graphcool server
  * `gcf playground` - Opens a web browser on a page that lets you explore and
    test your API
* Configuring Graphcool (Through the `graphcool.yml` file)
  * `types` - Provide the location of the API schema
  * `functions` - Define resolvers and hook functions
  * `rootTokens` - Define what functions have root privileges, this is used to
    perform further data mutations and querying data normal users can't (User
    password hash etc.)
  * `permissions` - Define permissions for data CRUD operations, for example,
    specify that only logged in users can create posts, create comments and
    vote. Or that no one it allowed to delete posts.
