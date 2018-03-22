# Iteration Stages

## An Explanation of each Iteration Stage

I decided to split up the implementation stage of building Corum up into 3 main
parts. The reasoning behind this decision was because I thought it would be much
easier if I could focus on a single part of the application at a time instead of
switching contexts. Also, it would have been almost impossible to develop the
entire frontend before I have implemented the API as I would have had nothing to
connect the client to.

The first stage was to implement the frontend code up to the point until it
needed to interface with the API. This meant doing things like working out where
things should go on each page and what they should look like.

The second stage was to implement the API. This included writing the code for
the signup and login resolvers. Due to the fact that the API and the frontend
are independent services I did not need to think that much about the
implications of the API design might have on the frontend.

The third stage was to connect the client to the API. This involved writing the
code that made queries to the API and made mutations to the data. For example on
the signup page, when the user pressed submit, the frontend needed to send a
mutation to the API in order to create the account. I also needed to handle how
the frontend would handle the data returned from the API, for example if there
was an error (Maybe the email address the user entered is already being used).

The following sub sections describe each of these stages in more detail.

### Implementing a Dumb Frontend (Stage 1)

First of all I needed to develop a dumb frontend for the site. This would mean
that I wouldn't have to think about the layout and styling of the site as much
when this iteration was complete. Instead I could just focus on the logic of
making the site actually function. The first stages of this iteration can be
seen in the 1st prototype I made.

This meant that in the following stages I could easily just connect HTML
elements such as buttons and links to JavaScript functions.

There wasn't much to test at this stage, all of the page looked how I wanted
them to look, and of of the links worked correctly. The majority of the testing
for Corum needed to be done when implementing the API and connecting it to the
frontend.

### Implementing the API (Stage 2)

Next, I needed to implement the API that Corum's client relies on for data.
Because I was using Graphcool it meant that I didn't need to worry about setting
up things like a database or a web sever, Graphcool handled all of that for me
by abstracting them away.

Instead, I had to think about how the data model defined in the design section
of this report would interface with the JavaScript code I needed to write to get
functionality like signup and login working. This was done by using hook
functions and custom resolvers.

The exact code for this iteration stage can be found in the API code analysis
section of this report.

To test the API I used a tool called `graphql-playground`. This tool is
available standalone but it also comes bundled with Graphcool. This meant that I
didn't have to set it up to test the API. When visiting the API endpoint
returned by Graphcool in a web browser it displays the `graphql-playground`
interface. This interface allows you to make queries and mutations to the API
without writing any code.

The following GIF shows how it works.

Location: **`images/gifs/iterations/graphql-playground.gif`**

![graphql-playground example](images/gifs/iterations/graphql-playground.gif)

The left hand side of the screen is where you write the query / mutation and the
right hand side of the screen is the result of that query / mutation that is
returned from the server. The play button (`|>`) in the top middle is pressed to
execute the query / mutation.

For example, I needed to implement a hook function that set the initial vote
count of every post to 0 to ensure that the client could fake the amount of
up-votes a post had.

To test this I could simply try all of the different cases that the client code
provide:

The client doesn't provide a `voteCount` field:

Location: **`images/gifs/iterations/post-test-1.gif`**

![Post Test 1](images/gifs/iterations/post-test-1.gif)

The client provides `voteCount` but it is `null`:

Location: **`images/gifs/iterations/post-test-2.gif`**

![Post Test 1](images/gifs/iterations/post-test-2.gif)

The client provides a `voteCount` field that is not `0`:

Location: **`images/gifs/iterations/post-test-3.gif`**

![Post Test 1](images/gifs/iterations/post-test-3.gif)

The client provides a `voteCount` field that is `0`:

Location: **`images/gifs/iterations/post-test-4.gif`**

![Post Test 1](images/gifs/iterations/post-test-4.gif)

As you can see in all of these examples, `voteCount` is returned as `0` in all
cases, as intended.

### Connecting the Client to the API (Stage 3)

After implementing the API and testing that it worked at expected I needed to
connect the frontend and the API together. This meant implementing the
following:

* The `Header` component changes state on login
* The `Navigation` component loads in all subforums and changes on login state
* The signup page creates a user and logs them in (with validation)
* The login page logs a user in if their details are correct (with validation)
* The subforum page loads the posts from the correct subforum
* The post page loads post details (name, author, content, comments etc.)
* The new post page creates a new post and redirects the user to the new post
* The new subforum page creates a new subforum and updates the navigation

This was all made pretty easy because of `graphql-playground`. It meant that I
could construct the queries and mutations for the client in
`graphql-playground`, then once I knew that they did what I wanted them to do I
just converted these queries and mutations into JavaScript code. This meant that
I could be confident that the frontend was fetching the correct data from the
API.

As this was the final iteration stage it meant that I could test everything just
by visiting the site in a web browser and checking that everything worked
correctly as set out in Corum's success criteria. This testing is shown in great
detail in the evaluation section of this report.

## Iteration Stages shown Visually

As explained in the previous section, the use of a version control system allow
me to go back in time in the development of Corum. This allows me to show
different visual iterations of the client codebase. There are 5 main visual
iteration stages that can be seen during the development process. The following
sub sections will show what the project looked like at that stage as well as a
short description of what had been achieved.

Due to the fact that the code base it so large, even in early prototypes, I have
left out displaying any code in this report from each stage. As mentioned
before, the entire codebase and all of its history is available to view online
in Github. I have tagged each prototype in the Git repository so that the state
of the codebase can been viewed exactly how it was.

### Prototype 1

Location: **`images/gifs/iterations/prototype-1.gif`**

![Prototype 1](images/gifs/iterations/prototype-1.gif)

This was the first iteration milestone that was reached. The goal of this
iteration was to implement a dumb frontend. This means that it was just to get
the HTML and CSS correct for the basic layout of all pages. All of the
components described in the design section of the this report were created and
layed out correctly on the page. This stage was very basic, for example the
`sign-up` and `login` buttons didn't do anything.

Also to note is that this prototype was created with React and not Vue, the view
library that I ended up using. At the end of this prototype, as explained
before, I didn't like how developing Corum in React felt.

### Prototype 2

Location: **`images/gifs/iterations/prototype-2.gif`**

![Prototype 2](images/gifs/iterations/prototype-2.gif)

In this prototype, I switched from React to Vue. This change can only be seen by
looking at the actual code. I had to convert all of the existing React
components over to Vue.

Also in this prototype I implemented a dumb version of the navigation. Each item
in the navigation correctly linked to the right subforum page, however the data
it was acting on was actually just a mock of the data the API should have been
returning. This was because at this stage in development I hadn't even started
to code the API. I also implemented a basic subforum page that just read the
subforum name from the url and displayed it. This was useful as it ensured that
the routing library was working correctly. Another thing that I added was basic
signup and login pages. These pages were not connected to anything, and the
signup and login buttons did nothing.

### Prototype 3

Location: **`images/gifs/iterations/prototype-3.gif`**

![Prototype 3](images/gifs/iterations/prototype-3.gif)

In this prototype I worked on the look and feel of the site as a whole. Instead
of using the grey scale colour scheme that the previous prototype was using I
used a white, blue and green colour scheme. I also added some drop shadows and
animations to give the site depth and make it feel more natural to use. I
applied this design to the login and signup pages as well. I think this
prototype was important as it made Corum feel a lot more professional and
modern.

You can also see that I removed the mock data that I was using in the previous
iteration and added a search bar in preparation for implementing the API into
the client.

### Prototype 4

Location: **`images/gifs/iterations/prototype-4.gif`**

![Prototype 4](images/gifs/iterations/prototype-4.gif)

This prototype has a lot less changes that the previous ones. I removed the
animations from the site as I thought it made the site feel less responsive. I
think that it is more important for the user to access the information they want
in the shortest amount of time rather than having a lot of animations.

Also, although you cannot see it visually, I added a lot of code for the client
to communicate with the API. The issue at the time was that the API was not
quite finished, so it wasn't possible to load the data. The reason for this is
explained later in the 'problems experienced' section as it was to do with the
fact that I hadn't thought about everything I needed to.

### Production Build

Location: **`images/gifs/iterations/production.gif`**

![Production Build](images/gifs/iterations/production.gif)

This is what Corum looks like in its current state. Here you can see the client
correctly communicating with the API. By this stage all of the success criteria
set out had been met. To find out more about the final build of Corum visit the
evaluation's testing section where I have recordings for every feature of Corum,
showing that it all works as expected.
