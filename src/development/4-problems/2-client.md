# Client Problems

## Managing Login State

### The problem

When the user logs in, a lot of the application has to react to this change in
state, for example, the buttons that are displayed in the header need to change,
the navigation needs to change how it works and users should be allowed to
create new posts and create comments on posts etc. Simply put, this change
effected a lot components in the component tree that were not close to each
other which meant that it was hard to propagate the state change through to all
parts of the application.

### The solution

Instead of updating the local state of each component individually when a user
logged in, I used the global state store that I had already added to Corum's
frontend to persist the users login details so that when the user revisited the
site another time they would still be logged in. I realised that as the store
contained information about the logged in user, it could also be used by any
other component that subscribes to it to know whether it should show the logged
in or logged out state. This meant that instead of updating each local component
state all I needed to do was update the global store and the components would
automatically be notified of the state change. This approach to the problem made
the code that depended on this state much more maintainable. This is because if
I added another component that needed to use this state I didn't have to touch
the login page code, I instead just needed to subscribe to the login state
available in the global store.

## Handling Errors Returned from the API

### The problem

If the API was given incorrect data or duplicate data then it would throw an
error, for example, if the user gave an incorrect email address or an email
address that was already in use, then it would throw an error. The issue with
this was that I had yet to figure out how to detect if the API returned a
successful response or if it returned an error. This meant that the frontend
would get into a broken state and not alert the user if an error occurred.

### The solution

It turns that it is defined in the GraphQL Spec that when an API operation is a
success it returns a JSON object with a root `data` field. It also defines that
when an API operation fails it returns a JSON object with a root `error` field.
This means that all the client needs to do to check for an error if check if the
`data` field is defined or if the `error` field is defined. For example the
check `data == undefined` or `error != undefined` would evaluate to true if an
error occurred.

## Updating Local State Based on API operations

### The problem

When the client performs an API operation, the UI has to wait for the API to
respond before it updates itself. This sounds reasonable as how would the UI
update its local state without knowing what the APIs response is? However, in
some situations, the data the API returns is only used to validate that the API
accepted the data the client sent to it. For example, when a user posts a new
comment, the client already has all of the information it needs to display the
new comment in the UI (The content of the comment, the author, and the post the
comment was posted on). The API is only needed in this operation to save the
comment to the database for when the user returns to the post another time and
for other users. This is also true for when a user votes on a post. This means
the UI doesn't seem very responsive as there is a considerable delay between,
for example, clicking the button to post the comment and it displaying in the
comments section. It would be good if these predictable API operations didn't
have to wait for the API response in order to update the local state with data
it already has.

### The solution

I figured out that the GraphQL client I am using for Corum (Apollo) has a
feature called `optimisticResponse`. This allows me to specify an object that
will be used to update the local state before the API responds. Also, when the
API does return the real data response, the UI updates the local state if it is
different from what the `optimisticResponse` was. Examples of
`optimisticResponse` being used in Corum can be found in the Code Analysis
section of this report.
