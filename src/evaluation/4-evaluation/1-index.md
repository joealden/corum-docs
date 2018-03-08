# Final Evaluation

## Success of the Solution

* Reference to test data
* Cross reference test evidence with success criteria and evaluate how well the
  criteria has been met. If any have not been fully met, comment on how they
  could be met with further development.
* One major one I can think of now (Not currently in the success criteria) is
  that the ergonomics of deployment is not the best. One solution to this would
  be to move the API to Prisma and offer an option to host the API and the
  client on the same machine with a single command.
* Could also talk about the possibility of adding GraphQL subscriptions to
  provide live updating info for the following:
  * New posts coming up on the subforum page
  * Vote count updating both on subforum and post page
  * New comments coming up on post page
  * Possible new subforums as well?

## Limitations and Maintenance

* Mention the fact that I did not get around to implementing the automated
  tests. This was because Nuxt, the client framework I am using, makes it hard
  to build robust tests at the moment. In future version of the framework, they
  have said that they will address this issue, and it will be easier to create
  robust tests. This will impact maintenance because when refactoring, adding
  and features and code, it will not be nearly as easy to tell if those changes
  have broke any other code depending on them.
* Talk about good code comments
* Talk about how this report itself could be used by myself and others to get
  familiar with the project.
* Talk about how these limitations could be fixed
