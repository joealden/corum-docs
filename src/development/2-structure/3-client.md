# Client Architecture

## Vue

* Reference analysis and design sections
* Why use Vue over react vs nothing

### Why use Vue?

* Abstracts away DOM operations
* Provides prebuilt solutions for common uses:
  * Routing (`vue-router`)
  * State Management (`vuex`)

## Nuxt

* Describe what nuxt is
  * Framework built on top of nuxt
  * Inspired by next.js for react
  * Intends to provide a better developer experience

### Why use Nuxt?

* Abstracts away a lot of configuration and boilerplate code
* Bakes in routing and state management
* Provides server side rendering (Makes initial load faster and allows for
  easier SEO)

### How Nuxt Works

* CLI (Command Line Interface)
  * `nuxt` - Starts the development server
  * `nuxt build` - Builds the client for production
  * `nuxt start` - Starts a production server (Files must have been built)
* Folders used by nuxt
  * `layouts`
  * `pages`
  * `static`
  * `store`
* nuxt modules
  * `apollo`
  * `dotenv`
  * `font-awesome`
  * `pwa`
* nuxt.config.js

While nuxt provides a great abstraction on top of Vue and related libraries, it
is also very customisable and extensible. Nuxt provides a central way to
configure itself, through a file located at the

## Apollo

* Describe what Apollo is (Reference design)
  * A GraphQL client that provide the following:
    * Middleware hooks (Used to add `Authentication` header when a user is
      logged in)
    * Data caching (Means less network fetches and faster responses)
    * Bindings for Vue (with the `vue-apollo` library)
* The way the client communicate with the API

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
