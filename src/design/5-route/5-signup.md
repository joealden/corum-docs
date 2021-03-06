# Signup Page

## Client Side

While the 'Login Page' section describes how the system will log the user into
the site, it doesn't say how users can create accounts. This will be done
through the signup page. Just like the login page, this page will be accessible
either by clicking on the `signup` button in the header, or from the url bar.
(At the path `'/signup'`) Also like the login page, the user will not be able to
visit the signup page if they are already logged in.

The signup form will contain the following:

* The Corum logo
* An email address field
* A username field
* A password field
* Another password field to verify that the user has type in their password
  correctly (Verification will be needed to check that they are the same)
* A signup button
* A message and link directing the user to the `login` page if they already have
  an account.

The signup button will only be enabled (clickable) if an email address has been
entered and both password fields have matching data in.

If the user successfully creates an account, (If there are no errors with the
data they entered, for example, an error would be an incorrect email or a user
with the same email or username already exists) then not only will the account
have been created, they will also be logged into the site with the data that
they entered in the signup form. This means that the same state changes occur as
mentioned in the 'Login Page' section. (Ability to create posts, comments etc.)

## Server Side

As mentioned in the 'Login Page' section, the graphcool framework doesn't
provide a user authentication system. Please read the 'Login Page' section for
more context of this subsection. (For information about resolvers etc.)

As well as the `authenticateUser` mutation that is created for the `login` page,
there will need to be a `signupUser` mutation to create an account.

Here is the algorithm for the `signupUser` resolver (This is basically the
pseudo code for the API signup flow):

* Expose a GraphQL mutation called `signupUser`
* This mutation will have the following inputs and outputs:
  * Inputs:
    * `email` -> type String (The email address associated with the account that
      will be used to login in the future)
    * `username` -> type String (The username of the user that will be displayed
      to other users in things such as posts and comments)
    * `password` -> type String (The password that will be hashed and stored,
      then checked against when the user logs in in the future)
  * Outputs:
    * `id` -> type ID (ids are generated by graphcool and are unique, the user's
      ID will be used to determine the state of the UI)
    * `token` -> type String (A JWT (JSON Web Token) that will be stored to be
      later used to authenticate the user when making requests such as to create
      a new post. For more information about JWTs, look at the 'Login Page'
      section)
* It will first verify that the email address is valid (Using the library
  `validator`)
  * If it is not a valid email, return an error to the user that the email is
    invalid
* Now that we know the email address is valid, we must check if a user has
  already registered with the same email address
  * If an account already uses this email address, return an error to the user
    that the email address is already in use.
* Now that we know the email address is also not already taken, we must also
  check if a user has already registered with the same username
  * If an account already uses this username, return an error to the user that
    the username is already in use.
* Now that we know a user with either the same email address or username doesn't
  exist, we can start creating the account.
  * First, we need to hash and salt the password before storing it, as storing
    password in plain text isn't a good idea for obvious security reasons.
  * Next, the user details actually need to be stored on the server, so a new
    User type is created.
    * This User type contains the email address, username and hashed password,
      as well things like the users generated ID.
* Now we can return the user the data that they requested.
