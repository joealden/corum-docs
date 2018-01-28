# Header

This component will have 2 different states. 1 for when the user is logged in,
and 1 for when they are not.

When the user is logged in, the component will render a greeting message, as
well as a `Logout` button. (As shown [here](#new-post)) When pressed, the
`Logout` button will execute a function that logs the user out of the site.

When the user is not logged in, the component will render 2 buttons. The 1st
button will be a `Sign Up` Link component that links to `'/signup'`. The 2nd
button will be a `Login` Link component that links to `'/login'`.
