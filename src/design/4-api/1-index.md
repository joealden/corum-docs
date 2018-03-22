# API Design

As I have discussed in the analysis section of this report, I will be using
GraphQL to develop the API of Corum. If you haven't heard of GraphQL before,
putting it very simply, it is a more flexible and powerful alternative to REST
style APIs.

Some of the reasons I am using GraphQL to develop my API over REST are:

* It allows the server to define what data the client can access, and then the
  client can choose the information it wants
  * This is unlike REST APIs, as they usually define API endpoints that are for
    a single client (for example, the mobile site only)
* The way GraphQL is designed allows for a large tooling eco-system to be built
  around it
  * For example, I will be using a GraphQL Client library called Apollo that
    does things like caching and fetching automatically
* It is easier for the server to add and remove data that clients can access
  without breaking existing client that depend on it

If you would like to find out more about GraphQL, I recommend the following site
as a good resource to get started.
([howtographql.com](https://www.howtographql.com/))

To more quickly develop a production standard GraphQL API, I will be using a
framework called Graphcool. The framework abstracts away some of the complexity
that comes with developing a GraphQL API, such as database management, data
access permissions etc. The best feature of graphcool is that is automatically
generated a CRUD (Create, Read, Update and Delete) interface to the schema
defined. This is very much like how an SQL database works, however instead of
using SQL, we use GraphQL to define what data we want. This allows me to
declaratively design my API, without having to think that much about how
everything works under the hood.

As well as abstracting away complexity, graphcool allows you to easily extend
its capabilities. For example, I will extend graphcool to do things such as
create and authorize users, initialize data so that the client doesn't have to,
(e.g. Set vote count of a new post to 0 automatically) and do some validation on
the data (e.g. Ensure a user can only vote once on a single post).

For more information about graphcool, visit there website here.
([graph.cool](https://www.graph.cool/))

## Flow Diagrams of API Actions

Here are two flow diagrams that I have made to help me visualise some of the
operations that the API will make. I decided to focus on creating flow diagrams
instead of writing pseudo code because flow diagrams create a clearer picture in
my head when I go to implement the operation.

### Signup Flow

This flow diagram describes what the API should do depending on the data it
receives from the client when a user attempts to signup.

![API Signup Flow](images/api-signup-flow.png)

### Login Flow

This flow diagram describes what the API should do depending on the data it
receives from the client when a user attempts to login.

![API Login Flow](images/api-login-flow.png)
