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

This section will explain how to use Nuxt in the context of Corum.

#### The Command Line Interface (CLI)

* `nuxt` - Starts the development server
* `nuxt build` - Builds the client for production
* `nuxt start` - Starts a production server (Files must have been built)

#### The Directory Structure

* `layouts`
* `pages`
* `static`
* `store`

#### Nuxt modules used by Corum

* `apollo`
  * adds graphql-loader automatically
* `dotenv`
* `font-awesome`
* `pwa`

#### Nuxt Configuration (`nuxt.config.js`)

While nuxt provides a great abstraction on top of Vue and related libraries, it
is also very customisable and extensible. Nuxt provides a central way to
configure itself, through a file located at the root of the project called
`nuxt.config.js`.

...

## Apollo

* Describe what Apollo is (Reference design)
  * A GraphQL client that provide the following:
    * Middleware hooks (Used to add `Authentication` header when a user is
      logged in)
    * Data caching (Means less network fetches and faster responses)
    * Bindings for Vue (with the `vue-apollo` library)
* The way the client communicate with the API

## CSS

* Why using stylus as a pre-processor
* Used by putting `lang="stylus"` as an attribute in `style` tag of Vue SFCs
* stylus-loader configured automatically by Nuxt

## Production vs Development

* Production vs development settings
  * Optimisations
    * Code minification
    * Turn off dev help
      * ESLint overlay (If something breaks, don't say exacting why)
      * Vue developer tools (That lets you explore info state)
      * Logging errors to the console (Why you don't want that in production)
      * Changing error page (Why)
    * Turn on service worker (With nuxt pwa module)

## Configuration

* .env file
