# API Problems

## Creating User Accounts and Later Authenticating them

### The Problem

For a user to perform protected actions such as creating a post, creating a
comment, or voting on a post, the user must have an account and be logged into
it. When implementing a login system it is important to consider carefully how
the user's password is stored due to its sensitive nature. I wanted hash and
salt the password before storing them in the database, however I was not sure
how to do so.

### The Solution

I knew of a password hashing algorithm called 'bcrypt' that provides built in
salting functionality and found out there was an implementation of it in
JavaScript. This library exposes functions that allowed to create and compare
hashes very easily. When the user signs up, the password they give is hashed and
stored in the database. When the user attempts to login, the password they give
is hashed and then compared against the hash stored in the database. If they
match, the user entered the correct password. This library made it easily to
safely store the user's password.

## Setting the Initial Vote Count of a Post to 0

### The Problem

When an authenticated user creates a post, the client sends a GraphQL
`createPost` mutation to the API and the post gets saved to the database. In
this case, the values that the client sends are predictable, as the code was
written by me. However it is possible for a malicious user to attempt to send
mutations to the server that are not predictable. In the case of creating a
post, this was an issue because it meant that if the API did not validate the
mutation it received, a malicious user could initialise a post with an arbitrary
amount of votes. This is a big issue as the whole point of Corum's vote system
is that it is democratic. If a malicious user was allowed to perform such an
action then it would break this system.

### The Solution

Through the use of hook functions, Graphcool allows you to modify requests
before they touch the database. As mentioned previously, hook functions can be
assigned to act only on specific data types. In this case I wanted to modify all
requests that attempted to mutate a `Post` type. So to ensure that the vote
count of posts could not be initialised to any value I modified these requests
by setting the `voteCount` property to `0`, effectively disregarding any value
set beforehand in the mutation request. This simple hook function ensures that
all posts retain their integrity.

## Ensuring a User could only Favorite a Subforum once

### The Problem

The API schema contains a data type named `Favorite` that is created when a user
favorites a subforum. This data type contains a `user` field and a `subforum`
field to keep track of what user favorited what subforum. In the context of
Corum it only makes sense for a user to be able to favorite a subforum once.
Graphcool provides a way to specify that a field should be unique in the current
data set. For example in Corum, this is used to ensure that the same username or
email address is not used by more than a single user. The issue is that
Graphcool doesn't currently provide a way to specific that two fields need to be
unique which is required in this scenario. For example, a user should be able to
favorite multiple subforums, and a subforum should be able to be favorited by
more than a single user, however a user should not be able to favorite the same
subforum more than once. The fact that this feature isn't available in Graphcool
means that I needed to find another way to achieve this functionality.

### The Solution

By making use of Graphcool hook functions I was able to achieve the correct
functionality. I figured out that if I assign a hook function to trigger before
a favorite is created, I could check if a favorite with that same `user` and
`subforum` already exists before allowing it to be written to the database. This
was done by querying the API with the data in the creation request. This
returned an array of `Favorite` instances that matched the search criteria. If
the array was empty it meant that I could be sure that no other favorite with
the same `user` and `subforum` fields already existed in the database. If the
array was not empty then a favorite with the request data already existed. In
this case, all I had to do was make the API respond with an error saying that a
favorite already existed with this data.
