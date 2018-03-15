# Iteration Stages

* Talk about iterative development style
* Talk about each develop cycle as a 'module' (testing at the end of each)

## Iteration Stages shown Visually

As explained in the previous section, the use of a version control system allow
me to go back in time in the development of Corum. This allows me to show
different visual iterations of the client codebase. There are 5 main visual
iteration stages that can be seen during the development process. The following
sub sections will show what the project looked like at that stage as well as a
short description of what had been achieved.

### Prototype 1

![Prototype 1](images/gifs/iterations/prototype-1.gif)

This was the first iteration milestone that was reached. The goal of this
iteration was to implement a dumb frontend. This means that it was just to get
the HTML and CSS correct for the basic layout of all pages. All of the
components described in the design section of the this report were created and
layed out correctly on the page. This stage was very basic, for example the
`sign-up` and `login` buttons didn't do anything.

Also to note is that this prototype was created with React and not Vue, the view
library that I ended up using. At the end of this prototype, as explained
before, I didn't like how developing Corum in react felt.

### Prototype 2

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

![Prototype 4](images/gifs/iterations/prototype-4.gif)

This prototype has a lot less changes that the previous ones. I removed the
animations from the site as I thought it made the site feel less responsive. I
think that it is more important for the user to access the information they want
in the shortest amount of time rather than having a lot of animations.

Also, although you cannot see it visually, I added a lot of code for the client
to communicate with the API. The issue at the time was that the API was not
quite finished, so it wasn't possible to load the data. The reason for this is
explained later in the 'problems experienced' section as it was to do with the
fact I hadn't thought about everything I needed to.

### Production Build

![Production Build](images/gifs/iterations/production.gif)

This is what Corum looks like in its current state. Here you can see the client
correctly communicating with the API. By this stage all of the success criteria
set out had been met. To find out more about the final build of Corum visit the
evaluation's testing section where I have recordings for every feature of Corum.

## A Detailed Explanation of each Iteration Stage

Due to the fact that I cannot show the iterations visually of non visual
development, this section explains these iterations in detail.

### Implementing a Dumb Frontend

* Implementing a dumb frontend (Mostly coding with just HTML and CSS at that
  point)
  * The landing page
  * The signup and login page
  * The subforum page
  * The post page
  * The new post page
  * The new subforum page

### Implementing the API

* Implementing the API
  * The `Subforum` datatype
  * The `Post` datatype
  * The `User` datatype
  * The `Comment` datatype
  * The `Vote` datatype
  * The `Favorite` datatype

### Connecting the Client to the API

* Implement the logic of the frontend to communicate with the API
  * The `Header` component changes state on login
  * The `Navigation` component loads in all subforums and changes on login state
  * The signup page creates a user and logs them in (with validation)
  * The login page logs a user in if their details are correct (validation)
  * The subforum page loads the posts from the correct subforum
  * The post page loads post details (name, author, content, comments etc.)
  * The new post page creates a new post and redirects the the new post
  * The new subforum page creates a new subforum and updates the navigation

## ??

* Ensure that the moderator knows that it all works as it should
