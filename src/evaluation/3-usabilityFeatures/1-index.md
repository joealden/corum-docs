# Usability Features

Wikipedia defines usability in the context of software engineering to be the
following:

> achieve quantified objectives with effectiveness, efficiency, and satisfaction

This means that when a user wants to perform a certain action on Corum it needs
to meet that criteria. It needs to be effective, efficient and satisfying. The
following sub sections will explain the usability features of Corum and how they
meet this criteria. Usability testing was included in the post development
testing section.

## Load Times Between Actions are Small

Through the effective use of libraries, frameworks, and technologies, Corum is
able to have very fast response times between actions such as navigating between
pages, creating new posts and logging in to the site.

Here are a few examples of how the frameworks, libraries, and technologies
chosen allow Corum to achieve this:

* Vue - Allows for client side routing and automatically batches DOM operations
* Nuxt - Performs server side rendering of pages as well as automatically
  optimising, minifying and compressing code sent to the user's browser
* GraphQL - Allows a client to specify exactly the data it needs from an API and
  no more, meaning that unneeded data is not sent to the user
* Apollo - Responses from the API are cached which means data load times are
  reduced

These fast response times mean that a user's visit is effective, efficient and
satisfying because they do not have to wait around for data or pages to load.

## Search Results are Shown Almost Instantaneously

As shown in the navigation testing GIFs, when a user searches for a subforum in
the navigation, the search results are calculated and displayed extremely
quickly. This usability feature has a similar affect to the one mentioned in the
above section, it means that instead of having to wait for search results, the
user can search for the subforum they want and navigate to it. The user doesn't
have to wait for search results to appear.

## Optimistic UI updates

Optimistic UI updates are where the client creates a placeholder piece of data
while it waits for a response from the API and updates the UI to show that
instead of waiting for the data response, which could be a while depending on
the user's network connection. This feature provided by Apollo has already been
discussed in detail in the development section of this report. The reason this
is good is because it means the user is provided with near instant feedback from
the application. When an application is slow to update the UI in response to a
user's action it feels clunky. This can lead to an unsatisfactory experience
using the application. Also, if the client can predict the data that the API is
going to return then it would be inefficient to wait for the API to respond.

## Loading States

When a user is on a low end device or on a poor internet connection, loading
times for data can be noticeable (can be multiple seconds). It is important that
if the data is taking quite a long time to load, the user knows that something
is still happening and the application hasn't just crashed. This is usually done
by displaying a loading message or animation. When applications do not provide
this kind of feedback it can be frustrating as you don't know if the program has
crashed or not. When this happens frequently it can lead to an unsatisfactory
experience using the application. This is why it was important that Corum
provided feedback to the user when data was being loaded from the API.

Here is a list of where loading states are displayed to the user:

* On loading the navigation lists (all subforums and a user's favorites)
* On loading a subforum or a post
* Signup and login loading states

These loading states are an effective way to tell the user what is happening,
which can lead to more satisfactory usage of the application.

## Helpful and Easy to Understand Error Messages

When an error occurs in an application (whether it was intentional or not) it is
important that it handles the error correctly so the program does not crash and
it tells the user what has gone wrong. For example in the context of Corum, when
a user enters incorrect login details it is important that they are told that
they typed something in wrong instead of nothing happening or the program
crashing.

It is also important that users are given helpful error messages when
appropriate, for example on the signup page, if a user attempts to sign up with
a username that already exists it wouldn't be a good user experience to be told
that an error occurred but not what caused it. If the user wasn't told that the
username was the issue then they might think its another issue like their email
address has already been used or their passwords don't match. For this reason it
is important to provide actionable error messages.

Providing helpful error messages means that users can potentially see what they
have done wrong and correct it instead of being frustrated by the site for not
giving them enough information.
