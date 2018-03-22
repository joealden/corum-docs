# API Architecture

## Graphcool

As mentioned before in both the analysis and design sections, I am using the
library and service known as Graphcool. Graphcool provides an easily way to
create production ready GraphQL servers in a small amount of time. It acts
almost like a database, in that it provides a permanent data store as well as
CRUD operations to access and modify the data.

These CRUD operations are exposed through a GraphQL API. The way this works is
that it generates a CRUD API with GraphQL queries and mutations based on the
schema you give it. The schema can be found in the design section of the API.

### Why Graphcool?

Graphcool is like Nuxt for a GraphQL server. Nuxt provides useful abstractions
on top of Vue to provide a better developer experience and a faster development
cycle. Graphcool abstracts away a lot of the implementation details of the
GraphQL API and allows you to declaratively specify what data you want to store.

This means that I don't have to reinvent the wheel, and I have more development
time to spend on other things.

### How Graphcool Works

#### The Command Line Interface (CLI)

Like Nuxt, Graphcool provides a CLI (called `gcf`) that expose two important
commands:

##### `gcf deploy`

This command deploys the API to one of the available public Graphcool servers.
Under the hood, graphcool reads a file named `graphcool.yml` and builds the API
using it, then it sends this build bundle up to the server.

##### `gcf playground`

This commands opens a web browser at the URL of the deployed service and lets
you explore and test your API.

#### Configuring Graphcool

When running the `gcf deploy` command, Graphcool looks and reads a file named
`graphcool.yml`. This file contains all of the information Graphcool needs to
create the API server. The following sub sections will described what each top
level property does. For brevity, the entire contents of Corum's `graphcool.yml`
file is not shown in the code analysis section, however, like anything in Corum,
the full file can be found at the projects Github repository.

##### `types`

This property expects a string containing the relative path of the `.graphql`
file that contains the API schema. This file must be written in the Schema
Definition Language (SDL) that is part of the official GraphQL specification.

##### `functions`

This is where you specify custom resolvers and hook functions that are needed
for the API. The property expects an array of objects that describe what the
function does and where to find the code for it. Every function object should
have a `type` and a `handler` property. The `type` property is used to tell
Graphcool if the function is a resolver or a hook function. The `handler`
property tells Graphcool where to find the code for the function.

If the type of the function is a resolver, the function object should also
specify a `schema` string that points to the file that contains what data types
the resolver should expect and what data types it should return.

If the type of the function is a hook function, the function object should also
specify a `operation` property. This expects a string that tells Graphcool what
data type and what CRUD operation to trigger on. For example, Corum has a simple
hook function that initializes the vote count of a post to 0 when it is created.
The operation property of this hook function is `Post.create`.

##### `rootTokens`

The property expects an array of strings. Each string should map to an existing
function that was defined in the `functions` property. If the function is listed
under this property, the function is given the highest level of permissions
(Also known as root). This allows the functions to perform queries and mutations
to the API that even authenticated users cannot such as getting the password
hash for a user. This is needed because functions such as the `login` resolver
need to access this kind of sensitive data to work.

##### `permissions`

As the permissions section of the `graphcool.yml` file is explained in the code
analysis section, I will not repeat the same information. For this reason, look
at the code analysis section for an explanation of how permissions work.
