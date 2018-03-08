# Client Architecture

## Vue

As mentioned in both the analysis and design sections of this report, I am using
a library called 'Vue' to build my sites user interface.

### Why use Vue?

If you are not too familiar with modern web development, many libraries and
frameworks have been created to make it easier to build performant and
maintainable web applications. This is because the web has become more than just
static web pages, but the underlying APIs have not changed all that much. These
libraries and frameworks make the job of a web developer easier.

For now I will be focusing on the libraries that make building the user
interface easier. They do this by abstracting away operations such as manually
manipulating the DOM (Through something called a virtual DOM) as well as
providing reactive rendering capabilities amongst other things. This improves
the developer experience (DX) considerably, as user interfaces can be created
declaratively. This means that instead of saying how to do something, (Like
manually manipulating the DOM) you just say what you want to happen. This makes
the code easier to reason about.

The two most popular view libraries today are React and Vue. They both provide
the same basic set of features, but they expose these features to the developer
in very different ways. This means that the decision is almost entirely down to
personal preference. I have experience with both of these libraries and in their
current state I prefer Vue.

Vue provides a way of building UI components in a `.vue` file. These files are
known as 'Single File Components'. (SFCs) These files allow you to declaratively
write a components markup (HTML), logic (JavaScript) and styling (CSS). This
provides excellent DX as you only have to view a single file to understand the
entire component. This is an abstraction that React doesn't provide, as it one
of the contributing factors to why I prefer Vue over React.

When I started developing the client side of Corum, I actually started using
React. I migrated to Vue because I felt it was more intuitive and less verbose.

## Nuxt

Nuxt is a framework that builds on top of Vue and intends to provide a better
developer experience when building web applications using Vue. It is inspired by
a framework for react that tries to do the same thing called 'Next.js'.

### Why use Nuxt?

#### Build and deployment tooling is Included

When building modern web applications, there tends to be a lot of configuration
to get the build tooling working correctly. Some of the most commonly used build
tools such as webpack are renown for there fine control configuration. This is
good because it allows the developer to really customise the tools if they want
to. However most of the time developers don't need as fine grain control and
just want something that works without spending a lot of time setting it up.

Two very common tools that are used are 'Babel' and 'Webpack'.

##### Babel

This is what Babel has to say for itself on their website:

> Babel is a JavaScript compiler. Use next generation JavaScript, today.

This means that you can write modern JavaScript and have it compiled down into
code that can be understood by older browsers and node.js versions. This is
great because it means that you don't have to bring your developer experience
down to accommodate for the lowest common denominator.

Babel can be configured a lot. You can choose what syntax to support and what
version of ECMAScript to compile down to. For example, with the
`babel-preset-env` preset you can choose how many previous browser versions to
support.

##### Webpack

Webpack is the most commonly used module bundler for JavaScript projects at this
moment in time. Webpack is a tool that takes an entry point, creates a
dependencies tree, and bundles it to an output depending on how it is
configured.

Webpack's documentation puts it like this:

> It recursively builds a dependency graph that includes every module your
> application needs, then packages all of those modules into one or more
> bundles.

This sounds very abstract because Webpack is capable of a lot and is incredibly
configurable.

Through the concept known as 'loaders', different types of modules (For example,
`.js`, `.css` or `.vue` files) can be processed in a different way. An example
of a loader that would be used in conjunction with babel is `babel-loader`. When
configured to run against `.js` modules, this loader will run babel on every one
of them. Another example that would be used with Vue SFCs would be `vue-loader`.
When configured to run against `.vue` modules, this loader will convert it into
a module that can be understood by the browser, as no browsers understand the
`.vue` extension.

Through the concept known as 'plugins', tasks can be ran that are not module
specific, such as code optimisation and minification. An example of a plugin
used commonly is called `DefinePlugin`. This plugin allows you to configure
global constants at compile time. This could be useful when you need to include
something like a build version in your final bundle.

##### What Nuxt does for You

As evident from the above sections on Babel and Webpack, both of these tools are
very configurable. This can be an issue when you just want to start a project
and don't need to have fine grain control of the build tools. This can be
achieved by either copying a lot of boilerplate configuration code (which is far
from ideal) or using a framework like Nuxt that abstracts away the configuration
into the framework itself.

Nuxt provides sane defaults for both Babel and Webpack in the context of
building a Vue project. This brings the best of both worlds as the framework
makes it easier to get started by providing sane defaults and it also makes it
easy to configure the underlying tools if you need to.

Nuxt provides the following out of the box:

* The ability to write `.vue` SFCs (It configures Webpack to use `vue-loader`)
* Automatic code splitting (It configures a Webpack plugin to do this work)
* Transpiles ES6+ syntax (It configures Webpack to use `babel-loader`)
* CSS pre-processor support (It configures Webpack to use a few CSS loaders)
* Bundles and minifies (It configures multiple Webpack plugins to do this work)

#### Routing and State Management is Baked in

While Vue is an excellent view library, it is just that, a view library. This is
a good thing because it does one job and does it well. However, it would be nice
if one could extend Vue's functionality to provide more than just a view layer.
The developers of Vue thought of this and provided a way to do so through what
are known as 'Vue plugins'. While there are many plugins available, both
official and non-official, I am only interested in two for Corum. These are both
official plugins created and maintained by the Vue core team. They are called
`vue-router` and `vuex`.

`vue-router` provides a client side routing system where the developer can
specify routes as Vue components and link between them easily. Without client
side routing the routing would have to be done server side. The benefits of
client side routing are talked about in the 'Route Design' sub-section of the
design section of the report.

`vuex` provides global state management to the application. This is needed for
an application like Corum where different components (and often pages) need to
be able to access to the same data. This is made even better by the fact that
the global data store is reactive. This means that components can subscribe to
the store and be notified when the data they want changes. This is useful in
many situations. An example in Corum would be where the login page updates the
user's login state, and components and pages need to display different
information depending on if the user is logged in or not. For example, the
header component of the UI needs to update to display a `logout` button if the
user logs in.

The issue is that if I want to use these plugins in my code, I have to write a
lot of configuration and boilerplate code to get them working at all. Nuxt
provides both routing and state management based on `vue-router` and `vuex` out
of the box. This means that Nuxt handles all of the configuration and means that
I don't have to write boilerplate code that makes my code harder to reason
about.

Nuxt also makes routing file based instead of it being done through a
configuration file. How this works is explained in the below 'How Nuxt Works'
section.

#### Server Side Rendering without the Hassle

In applications built with libraries like Vue, the majority of the markup is
generated on the client side after the initial HTML page has been loaded. For
example, you might send a HTML document that looks like this:

```html
<!DOCTYPE html>
<html>
<head>
  <title>React or Vue Application</title>
</head>
<body>
  <div id="root"></div>
  <script src="bundle.js"></script>
</body>
</html>
```

At the end of the body element is a script tag that loads a file called
'bundle.js'. This file will contain the the Vue runtime as well as all of the
files that you as a developer created. Given the element selector that the
developer passed to Vue (In this case `#root`), Vue will mount itself at that
location. From there, Vue will add and remove elements to and from the DOM.

While this is all happening, all the user is seeing is a blank page. The issue
is that the time that the page is blank can be quite long depending on the users
hardware. This is because the device has to parse and execute the JavaScript in
`bundle.js` before any visible markup is added to the DOM by Vue.

Another issue is that some search engines that use crawlers don't execute
JavaScript. This means that all the crawler will see is the above HTML and
nothing more. This could severely hurt the SEO of the site on certain search
engines.

Some users also choose not to execute unknown JavaScript code by changing a
setting in their browser. Just like the crawler, these users will not see
anything on the page.

The way to solve this is to use a technique known as Server Side Rendering
(SSR). When SSR is implemented, the server goes through a similar process to the
one above that happens on the client and saves the markup outputted by Vue. This
markup is then what is sent to the user instead of just the application skeleton
shown above. The SSR version of the code above might look something like this:

```html
<!DOCTYPE html>
<html>
<head>
  <title>React or Vue Application</title>
</head>
<body>
  <div id="root">
    <main>
      <h1>Home Page</h1>
      <p>This is the home page.</p>
    </main>
  </div>
  <script src="bundle.js"></script>
</body>
</html>
```

Here you can see that the element that Vue mounts to (`#root`) contains some
markup when it is returned from the server. This means that as soon as the page
is parsed and loaded, the user will be able to see the markup. This means that
the initial perceived load time is faster. It also means that SEO is better as
the search engine crawlers that don't execute JavaScript can now see the correct
markup, as well as those users that have JavaScript disabled.

At the end of the body there is still a script tag that loads `bundle.js`. This
time `bundle.js` contains JavaScript that performs an action known as
'hydrating'. This is where Vue checks the markup inside the mounting element and
compares it to the markup that it would have inserted there. If they match, Vue
can be sure that the page has rendered correctly and can perform its reactive
updates when needed.

After this process has finished, the site acts just like it would when SSR was
not being used.

#### Module System

Nuxt also provides a 'module system'. This is similar to the Webpack plugin
system where developers can extend the functionality of the tool. This also
means that developers can publish Nuxt modules to be downloaded by others. In
the below section named 'How Nuxt Works' there is a sub section on what modules
Corum uses.

### How Nuxt Works

This section will explain how Nuxt works in the context of Corum.

#### The Command Line Interface (CLI)

Nuxt is actually just a CLI tool that exposes 3 important commands:

##### `nuxt`

This command starts the Nuxt development server.

The following happens in development mode:

* The server is reloaded every time a file changes
* Errors are logged to the console and on an overlay

These features are useful when developing but are not needed or wanted when
deploying the site to production.

#####`nuxt build`

This builds and bundles all of the sites files. This makes it ready to be hosted
on the production server.

##### `nuxt start`

This starts the production server. Before executing this command, it is
important that the site has been build with the `nuxt build` command.

The following happens in production mode:

* All errors are suppressed so that it is harder for an attacker to exploit a
  possible bug
* the files are minified and optimised.
* Vue developer tools are disabled for better performance
* The service worker added by the PWA module is turned on

This makes the production site faster and more refined.

#### The Directory Structure

Nuxt utilises a file based API system. Each of the following sections explains
what each directory means to Nuxt.

##### `layouts`

Nuxt has a concept of 'layouts'. A layout is a Vue SFC that can be used to make
multiple pages have the same base layout. When a page is rendered, Nuxt looks at
what layout it specifies and then renders the page inside of the specified
layout. You can choose where the page renders in the layout using the `nuxt` Vue
component.

As mentioned above, pages can say what layout they want to use. This is done in
the page's SFC under the `"layout"` property. For example, if a page specified
`"layout": "test"` then the page would use the `test.vue` SFC found in the
`layouts` directory. The `"layout"` property defaults to `default` if it is not
otherwise specified. Because Corum has a single consistent layout, only a single
`default.vue` exists. This means that all pages default to use this layout.

From the sketch shown in the 'Component Design' sub section of the Design
section, it is clear what components should be kept in this file:

* The `Logo` component
* The `Header` component
* The `Navigation` component

Then where the `Main Content` section is in the UI design sketch is where the
`nuxt` Vue component should be used to render the actual page.

##### `pages`

This directory contains all of the sites pages as Vue SFCs. Here is a diagram of
the structure of Corum's `pages` directory:

```
pages
├── index.vue
├── login.vue
├── new-subforum.vue
├── signup.vue
└── subforum
    └── _subforum
        ├── index.vue
        ├── new.vue
        └── post
            └── _post.vue
```

This directory acts as a file based router. This means that when Nuxt builds the
site for either development or production mode, it reads the structure of this
directory and creates page accordingly. For example, the `index.vue` file would
map to the route `/`, the `signup.vue` file would map to the route `/signup` and
the `subforum/_subforum/index.vue` would map to the route `/subforum/:subforum`.

From the examples given above, you can see that you can nest directories in the
`pages` directory to create deeper routes. Also, it is important to note that
any file prefixed with a `_` means that the value of this part of the route is
dynamic.

For example, in the final example above that maps to `/subforum/:subforum`, the
`:subforum` part of the route can be any string such as `/subforum/programming`.
The dynamic variable can be accessed from within the pages SFC, meaning that the
page can dynamically show different content from the same route depending on the
content of the dynamic part of it.

If you look back at the 'Route Design' sub section in the Design section of this
report, you can see from the diagram above that the files shown map to all of
the routes that are needed for Corum.

##### `static`

This directory is a place to store any asset that is linked statically in the
site in its HTML. The only thing Corum uses this directory for it to store the
favicon of the site.

##### `store`

This directory is used to store JavaScript files that are used to create the
global state store using `vuex`. This directory is optional. If the directory is
empty, then Nuxt will intelligently exclude `vuex` as a dependency, as if the
directory is empty, the global state store is not being used, and therefore is
not needed.

In the context of Corum, only a single `index.js` file is contained within it.
This file creates and exports a store that is used to keep track of the user's
data. A detailed explanation of this file can be found in the later sub section
named 'Client Analysis'.

#### Nuxt modules used by Corum

As mentioned above, Nuxt provides an extension point through the use of 'Nuxt
modules'. These are the modules that Corum uses that have been published by
others.

##### `apollo`

This will only make complete sense once you have read the section below titled
'Apollo'. In short, this module provides a very simple way to setup the Apollo
library that I am using to fetch data from the GraphQL API.

##### `dotenv`

This allows Corum to read in configuration options from a file named `.env` at
the root of the project though a global variable called `process.env`. There is
also a section below that will explain this in more detail named
'Configuration'.

##### `font-awesome`

This module injects a CSS library called 'Font Awesome'. This library provides a
large collection of SVG icons that Corum uses. These can be seen in a lot of
places, for example in the `login` and `signup` buttons.

##### `pwa`

This module provides a zero configuration Progressive Web App experience. This
includes things like adding a service worker to the site when in production mode
and more.

#### Nuxt Configuration (`nuxt.config.js`)

While nuxt provides a great abstraction on top of Vue and related libraries, it
is also very customisable and extensible. Nuxt provides a central way to
configure itself through a file located at the root of the project called
`nuxt.config.js`.

The details of Corum's Nuxt config are explained in the client's code analysis
section.

## Fetching Data with Apollo

As mentioned in the Design section of this report, the API is a GraphQL server.
This is instead of a similar comparable technique such as REST. To find out more
about the benefits of GraphQL, have a look at the 'API Design' sub section in
the Design section.

Because the API is a GraphQL server, the client needs a GraphQL client library
to communicate with the server. Communication with the server could be done
manually with `GET` and `POST` JSON requests to the API endpoint, however, just
like Vue provides a declarative interface to the DOM, GraphQL clients provide a
declarative interface for fetching data.

Even though GraphQL is a relatively new technology, there are quite a few
clients to choose from. Some libraries target React as it is the most popular
view library (Such as `urql` or `relay`). I will be excluding these options as I
want to be able to have a nice interface to the client through Vue.

These are the two choices that I considered:

* `graphql-request`
* `apollo`

The following sub sections will give a brief explanation of the pros and cons of
each library.

### `graphql-request`

This is the most lightweight option of the three. This is a benefit as the user
will have less code to download, parse and execute, however, this comes at the
cost of less features. Unlike the other two options, `graphql-request` doesn't
have cache implementation.

This is an issue for Corum as I want the data fetched to be cached so that if a
user requests the same data multiple times, multiple network requests are not
made. For example, if the user visits a post, goes to another subforum then
returns to the previous post, the post data would have to be fetched again even
though it hasn't changed. This is only an issue in `graphql-request`.

If I want to have a cache and use `graphql-request` then I would have to create
a wrapper around the library, however it would be wiser to just use another
library that has caching built in. This is because not only would it take time
to create the cache implementation that I could spend on other things, it would
also take away from how simple and lightweight the library is.

### `apollo`

This is currently the most popular GraphQL client library. I mentioned briefly
in the design section of this report that I was likely going to be using this
library.

Like `graphql-request`, Apollo Client is not bound to a specific framework (It
is framework agnostic). However, unlike `graphql-request`, it provides libraries
with bindings to popular view libraries such as React, Angular and Vue. This
makes it much more ergonomic to work with the library within the supported view
libraries. As I am using Vue as the view library for Corum, I will be using the
Vue bindings named `vue-apollo`.

Also, Apollo Client provides built in support for caching. This is good for the
reason stated above in the `graphql-request` sub section above. The way this
works in Apollo is described in the code analysis section of this report.

Another nicety that Apollo Client provides is the concept of middleware hooks.
In simple terms, middleware hooks allows the developer to execute arbitrary code
and to modify the data that passes through the library on a request or response.
This concept is used within Corum to add an `Authorization` header to every HTTP
request sent to the API server (If the user is logged in). This allows the API
server to verify that the user is allowed to execute protected queries and
mutations. The implementation code for this can be found in the code analysis
section.

## CSS

As mentioned previously, Vue allows you to develop in a format know as Single
File Components (SFC). These files are the ones with a `.vue` extension. They
basically allow you to write HTML, JavaScript and CSS in a single file. During
the build process, these files are compiled into plain JavaScript files that
browsers can understand. The way the file is written is like a HTML document.
The three top level elements that can exist in an Vue SFC are `template` (HTML),
`script` (JavaScript), and `style` (CSS). All of these tags allow for attributes
like normal HTML tags.

The two attributes that we are interested in are `lang` and `scoped`. These two
attributes are described in the following sub sections.

### `lang`

This attribute allows you to specify what language the code inside of the
element is written in. This attribute is allowed on all top level elements.

For example, you may want to use a HTML pre-processor such as Pug. This will
work by adding `lang="pug"` to the `template` tag, as well as installing the
correct package to process the code inside the element (In this case, the `pug`
package).

For an example for the `script` tag, you may want to use a compile-to-JS
language such as TypeScript. This will work by adding `lang="ts"` to the
`script` tag, as well as installing the correct package to process the code
inside the element (In this case, the `ts-loader` package).

For an example for the `style` tag, you may want to use a CSS pre-processor
language such as SASS. This will work by adding `lang="sass"` to the `style`
tag, as well as installing the correct package to process the code inside the
element (In this case, the `sass-loader` package).

### `scoped`

This attribute is only allow to be placed on `style` elements. This attribute
scopes the CSS inside the element to this component only. This means that if a
class is specified that changes the background colour, if it is applied outside
of this component, it will not get this styling. This works by adding a prefix
to the class name that ensures that it is only scoped the HTML inside the
component. This means that I don't have to think about CSS rules clashing with
each other and causing unwanted styling.

### In the Context of Corum

In the context of Corum, I apply the `scoped` attribute to most of my `style`
blocks. Also, I use a CSS pre-processor called Stylus. This is achieved by
adding `lang="stylus"` to the `style` tag and installing the `stylus-loader`
package. The reason I use a CSS pre-processor is because regular CSS has
problems that I don't want to deal with. This includes things like allowing
nested rules and auto-prefixing rules so that Corum works correctly in older
browsers.

## Configuration

Certain settings for Corum exist in the file named `.env` at the root of the
client project. The variables in this file can be accessed from any file through
`process.env`. During the build process, these variable values are inlined into
the files so that there is no performance hit. The following sub sections
describe what the two variable in this file do.

### API_ENDPOINT

This setting used when setting up Apollo Client. This expect the URL of the API
endpoint. This has been abstracted up into this file so that if someone else
wants to run a separate instance of Corum, the can easily change what API server
it points to.

Example: `API_ENDPOINT=https://api.graph.cool/simple/v1/corum`

### PROD

This setting is used a function called `logIfDev`. This function reads the value
of this variable and logs details to the console depending on if the value is
`true` or `false`. This code can be found in the code analysis section.

Example: `PROD=true`
