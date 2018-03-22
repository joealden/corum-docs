# Computational Methods Required

Here is a list of computational methods that could be used to create Corum, as
well as how they will be useful.

## Decomposition

By using [Vue](https://vuejs.org/), I will be able to break the UI down into
small, reusable components. This allows developers to create large projects
while keeping it manageable and maintainable. In the case of Corum, it would not
be manageable or maintainable to create the whole site in one large file. Not
only would it be a nightmare to work on, common code and components could not be
reused across different pages.

## Abstraction

Also with the aid of [Vue](https://vuejs.org/), I will be able to compose large
components from multiple smaller components. This means that the complexity of
each component can be abstracted away when taking a high level view of the
composite components. For example, each page of the site will be considered its
own component, but it will contain many smaller components such as the
navigation component and a main content component.

## Pattern matching

I will attempt to implement search functionality into the navigation part of the
application, this will require pattern matching. Also, as this project will make
use of client-side routing with
[vue-router](https://nuxtjs.org/api/components-nuxt-link), pattern matching is
required to direct the user to the correct page. For example, navigating to
`/subforum/:subforum` will render the subforum page component with the data from
the path variable `:subforum`.

## Sorting and searching

Related to the method above, searching will be used as the subforum navigation
will be searchable. This will most likely be achieved by using a regex (Regular
expression), or a composition of built in javascript string methods. Sorting
will also be used, as the user will be able to select the order in which they
see posts in the sub-forum. This will most likely be handled on the server side,
as in the end, I will want the subforum post list to be paginated. This means
that a sort would require a re-fetch from the server, as the client will not
have all the data at once. Graphcool provides an 'orderBy' parameter in their
queries, so it will not be too complex to implement.

## Use of Multiple Programming Paradigms

### Declarative

Vue allow me to develop components as
[SFCs (Single File Components)](https://vuejs.org/v2/guide/single-file-components.html)
This allows me to create my components declaratively and encapsulated, as the
HTML, JavaScript and CSS for that particular component will all be contained
within the single file. Also, as I am using Vue, I do not have to worry about
_how_ my components will get rendered to the
[DOM (Document Object Model)](https://en.wikipedia.org/wiki/Document_Object_Model),
I just tell Vue _what_ I want to render, and Vue will figure out the most
efficient way to do so.

### Functional

Programming in a functional style helps improve code maintainability,
readability, and [more](https://en.wikipedia.org/wiki/Functional_programming).
Also, the project will be easier to test (Due to things such as pure functions
not having side effects, and function composition allows for better testing in
isolation).

JavaScript provides great tools to build software in a functional paradigm, this
includes features such as:

* [First-class Functions](https://en.wikipedia.org/wiki/First-class_function) -
  This means that JavaScript treats functions the same as any other variable.
  Functions can be passed as arguments to functions, returned from functions,
  assigned to variables and stored in objects and arrays.
* [Arrow Functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) -
  Allows for concise function declarations and clean
  [function currying](https://en.wikipedia.org/wiki/Currying). For example:

```javascript
// Regular function expression - not curried
const concatRegular = function(string1, string2) {
  return `${string1} ${string2}`
}
const joinedTextRegular = concatRegular('Hello,', 'world!') // "Hello, world!"

// Arrow function expression - curried
const concatArrow = string1 => string2 => `${string1} ${string2}`
const joinedTextArrow = concatArrow('Hello')('world!') // "Hello, world!"
```

* Functional Array Methods
  * [`Array.prototype.map`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)
  * [`Array.prototype.filter`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)
  * [`Array.prototype.reduce`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)
* Immutability
  * [`const`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)
    (not perfect, reference only so objects + arrays can be mutated, use
    [`Object.freeze`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)
    (for both objects and arrays) or something like
    [Immutable.js](http://facebook.github.io/immutable-js/)).

## Real Time Data Processing

I will implement real time search functionality for the navigation, as well as
possibly real time sub-forum updates like updating the current amount of votes
updates without a page refresh. This will be achieved through the use of
[GraphQL Subscriptions](https://www.howtographql.com/vue-apollo/8-subscriptions/).
