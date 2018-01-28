# Automated Testing

These are the following types of tests I will write:

* Unit Tests
* Integration Tests
* End to End Tests (e2e)

I will be using the following software to automate my testing:

* [Jest (For unit and integration testing)](https://facebook.github.io/jest/)
* [Cypress (For e2e testing)](https://www.cypress.io/)

The following subsections will describe what each of these types of test are,
and examples of where they could be used.

## Unit Testing

Unit testing is where individual parts (units) of a program are tested in
isolation. For example, a single function or a single class method could be
considered a unit, and tested on its own. Sometimes, to test a unit in a
predictable way, its inputs and outputs (IO) may need to be whats called
'mocked' (faked).

Examples of mocking include the following:

* File system reads and writes
* Database reads and writes
* Network interactions

All of the above examples could fail and cause an error without the unit in
question being at fault.

For example:

* The OS might prevent a file from being written to or read from because its
  permissions have been changed or the file is locked by another program
* Database reads and writes require a database to be running, and for it to be
  consistent, it needs to be in the same state for every test
* The network connection may be disconnected, or it may be really slow, these
  factors could both effect the outcome of a test

It is evident from the examples above that mocking is about keeping
uncontrollable / unpredictable state predictable and consistent.

### Advantages

* Relatively easy and quick to write
* Quick to run
* Allows safer refactors
* Simplifies writing integration tests

### Disadvantages

* Does not necessarily represent how the program will act in the real world
* Can be difficult to draw the line of what to test and what not to test (Should
  simple units be left out of code coverage?)

### Example

An easy unit test to write would be the validation that happens on certain
pages.

For example, the signup page will have a validation function to check the
following:

* An email address has been entered
* A username has been entered
* The first password field has been entered
* The second password field has been entered
* The first and second password fields match

The function would expect the following inputs:

* Email address
* Username
* Password 1
* Password 2

The function would return the following output:

* boolean true if all is valid
* boolean false if something is not valid

In this case, I could test for the following:

* Call the function with inputs that should work and test that true is returned.
* Call the function with inputs that shouldn't work and test that false is
  returned.
* Call the function with edge case inputs such as a password containing only
  spaces.

## Integration Testing

While unit testing is testing in isolation, integration testing is testing that
units work together. This type of testing is usually done after unit testing.
The reason for this is that it is easier to narrow down bugs as they happen if
the unit tests are known to pass.

### Advantages

* They can find issues with a system that unit tests alone cannot
* The test cases are closer to how the code is actually executed in production

### Disadvantages

* Writing them properly is harder than writing unit tests

### Example

An example of a possible integration for Corum would be testing that the UI
state is correct depending on the logged in state.

These are couple of things that are supposed to happen when a user is logged in:

* The header should display the username of the user that is logged in, and a
  logout button
* A 'favorites' section should be visible in the navigation along with the 'All
  Subforums' section

In this example, these are the steps that would have to happen in this
integration test:

* The global state store would have to be queried in order to test that a user
  is logged in
* The UI would have to be checked that it is in the correct state depending on
  what is currently in the store

As evident from above, this tests requires that multiple units are working
together to provide the correct output.

## End to End Testing

As suggested by the name, end to end testing (e2e) is where a complete user
story is tested.

In the case of a website, the following would be considered e2e testing: A
button is pressed, which sends a message to the server, and the server responses
with another message.

In this example, the following sub-systems are tested

* The user interface
* The client side logic
* The server side logic

The following could also be tested depending on the test:

* A database action, such as reading or writing
* The server querying another server and getting the correct response

By testing all of these sub-systems, these tests ensure that when the code is
put into production, it will work as intended. Also, it is important to note
that e2e tests can be made up of 1 or more integration tests.

### Advantages

* Tests an entire user story, which means that it is as close to what the user
  will experience as possible

### Disadvantages

* Writing them properly is even harder than writing good integration tests

### Example

An example of a possible e2e test for Corum would be testing the entire user
login flow.

This is what is suppose to happen when the user goes to login:

* The user visits the login page at `'/login'`
* They fill out both the email and password field
* The client sends a message to the API server requesting to authenticate them

If the details are not correct:

* The server sends an error response to the client
* The client displays the error message to the user
* The client allows the user to try again

If the details are correct:

* The server sends back the data the client requested
* The client stores the the data sent back from the server
* The UI updates to reflect this change (Now in a logged in state)
