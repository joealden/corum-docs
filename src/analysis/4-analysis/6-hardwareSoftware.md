# Hardware and Software Requirements

In the following subsections, I will explain both how to host and view the site.
These two use cases will require different software and hardware.

## Hosting the Site

The project is based on a full JavaScript stack, so it is able to be developed
and deployed on any OS that Node.js supports. If you haven't already, download
Node Version 8+ [here](https://nodejs.org/en/download/current/) and install it.
This will give access to Node and NPM (Node Package Manager). Now your system is
ready to develop and / or deploy Corum.

To get a copy of Corum on your local machine, clone this repo using the
following command ([Git](https://git-scm.com/) must be installed):

```bash
git clone https://github.com/joealden/corum.git
```

### Development

[Nuxt](https://nuxtjs.org/) provides a pre-configured hot reloading dev server
(using webpack-dev-server under the hood), which means I can save a file and see
the resulting change instantly in my browser. This dev server also provides an
in-browser error overlay, which allows for easier debugging.

To start the dev server, run the following commands:

```bash
yarn
yarn dev
```

To view the site, navigate to `http://localhost:3000` in your browser.

### Deployment

To start the production server, run the following commands:

```bash
yarn
yarn deploy
```

## Viewing the Site

### Hardware

As mentioned in the section talking about the projects limitations, Corum will
not be actively made compatible with mobile devices (such as tablets and
smartphones), as it is currently only a prototype version. In the future, Corum
could be made to work on mobile devices as well. For this reason, it is
recommended that Corum is viewed on a desktop or laptop.

### Software

As Corum is a website, any modern browser on any modern Operating System will
work. As I am using reasonably new features such as
[CSS Grid Layout](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout),
it is recommended to view the site on the most up to date versions of Chrome,
Opera, Firefox or Edge. The reason I have chosen not to support older browsers
is because it would make the user experience of those using modern browsers
worse, as well as making it a lot harder to develop.

## Other Software Information about the Project

### Corum's Backend

I am planning to use [graphcool](https://www.graph.cool/) as my API and user
authentication backend. Graphcool is a BaaS (backend as a service) that combines
'serverless' (A service like AWS Lambda) and GraphQL (an API query language).

This will allow me to easily develop the client side of the application without
needing to worry about the implementation details of the GraphQL backend that it
fetches data from. Graphcool is a free to use service for small sites, as shown
[here](https://www.graph.cool/pricing/).

### Libraries / Tools To Be used

**ALL** of the code and technologies that will be used for this project are open
source.

* **Language** - [ES2015](http://es6-features.org) +
  [ES2017](http://node.green/#ES2017) (JavaScript)
* **Runtime for development** - [Node.js 8.x.x](https://nodejs.org) and
  [Chrome](https://www.google.com/chrome/browser/desktop/index.html)
* **VCS
  ([Version Control System](https://en.wikipedia.org/wiki/Version_control))** -
  [Git](https://git-scm.com/) with [Github](https://github.com/joealden/corum)
* **Package Manager** - [Yarn](https://yarnpkg.com)
* **Task Runner** - [NPM Scripts](https://docs.npmjs.com/misc/scripts)
* **Framework** - [nuxt](https://nuxtjs.org/)
* **View** - [Vue](https://vuejs.org/)
* **Data Fetching (GraphQL)** -
  [Apollo Client](https://github.com/Akryum/vue-apollo)
* **Client-side Routing** -
  [nuxt-link (vue-router)](https://nuxtjs.org/api/components-nuxt-link)
* **Module Bundler** - [webpack](https://webpack.js.org/)
* **JS Compiler** - [babel](https://babeljs.io/)
* **JS Linter** - [ESLint](https://eslint.org/)
* **Testing** - [Jest](https://facebook.github.io/jest/)
