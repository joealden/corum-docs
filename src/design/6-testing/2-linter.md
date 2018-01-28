# Linter

During development, I will be using a utility called a linter. A linter is able
to statically analyse code and print out warnings and errors to the programmer.

A linter can catch errors such as:

* Using a variable that is not defined
* Missing a bracket (For example, in an if statement or in a function call)
* Attempting to reassign a constant
* Incorrect formatting (For example, using tabs instead of spaces)

As I am using JavaScript, I will be using the current most popular linter called
ESLint.

As quoted from ESlint's website:

> The pluggable linting utility for JavaScript

The reason ESLint has been so popular is the fact that it is 'pluggable'. This
means that it can be easily customised. In the context of a linter, this means
that rules can be turned off and on easily. For example, I prefer not to use
semicolons in JavaScript, so I can just tell ESLint to enforce that rule. This
makes it extremely powerful, as I can define exactly what I want it to warn me
about.

While this isn't exactly testing, it does help me catch and prevent potential
errors that could result in bugs.
