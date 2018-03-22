# Final Evaluation

In this section I will evaluate how successful the implementation of Corum was
by comparing it to the success criteria set out in the analysis section of this
report. I will also talk about the limitations of the solution as well as
possible maintenance issues.

## Success of the Solution

I set out a list of success criteria for the project in order to guide the
implementation of this solution in the analysis section of this report. Here is
a copy of this list along with comments on how well the solution met each point.
To avoid repeating information I will refer to other parts of this report for
evidence.

* The UI should resemble the layout seen in the 'GUI Design' section
  * When comparing the initial GUI design set out in the design section of this
    report to all of the GIFs in the final testing section I would say that this
    criterion has been met. While I have made small adjustments during the
    implementation period to do with styling the overall structure of each page
    remains the same.
* Users can click on subforums, and the site displays the subforum's posts.
  * This criterion has been met and the evidence for this can be seen in the
    final testing section.
* Users can click on posts, and the site displays the content of the post.
  * This criterion has been met and the evidence for this can be seen in the
    final testing section.
* Users can click on the `signup` button and signup to the site (Details saved
  to backend)
  * This criterion has been met and the evidence for this can be seen in the
    final testing section.
* Users can click on the `login` / `logout` button and login to / logout of the
  site (Check details against the backend)
  * This criterion has been met and the evidence for this can be seen in the
    final testing section.
* Logged in users can add & remove subforums to their `favorites`
  * This criterion has been met and the evidence for this can be seen in the
    final testing section.
* Logged in users can vote on posts
  * This criterion has been met and the evidence for this can be seen in the
    final testing section.
* Logged in users can comment on posts
  * This criterion has been met and the evidence for this can be seen in the
    final testing section.
* Logged in users can create new posts
  * This criterion has been met and the evidence for this can be seen in the
    final testing section.
* Posts are automatically deleted when a voting threshold is reached. This
  threshold will be something like when the votes reaches -10
  * This criterion has been met and the evidence for this can be seen in the
    final testing section. For testing purposes I set the threshold to -1
    instead of -10 so that I could see that the system was working whilst only
    having to be logged into a single user. In production this threshold would
    be changed back to -10.

## Potential Limitations

While I met all of the success criteria for the project, it does not mean that
the solution does not have some limitations. The following sub section will
outline some of the potential limitations of the solution:

### Scalability

Corum has only been tested with a relatively small amount of data, for example
during the testing I only had a data set this large:

* 4 user accounts
* 10 subforums
* 25 posts
* 40 comments

If this system was to be used on a live site with a substantial amount of
traffic, there would likely be a lot more data stored in the database. The issue
with not having tested the solution at scale could mean that when the data set
gets large, the application could start to get slow. For example, if there are
many users the API will have to do more work to find information such as if the
username or email address has already been used. Another example would be if
there are many posts (hundreds or thousands) that belong to a single subforum,
the solution in its current form would attempt to fetch all of these posts at
once which would be hugely inefficient. This could be fixed by implementing a
feature known as pagination.

The takeaway here is that if Corum was planned to be deployed to production, it
would be wise to think about how it would scale in the long term.

### Using the Site on a Mobile Device

As mentioned in the analysis section of this report, I did not focus on the
functionality of the site on mobile devices. However, as this is just a
prototype version of the site, it doesn't matter as much. If Corum was to be
deployed to production it would be wise to think about how it works on mobile
devices. This is because as more and more people are consuming the web on their
mobile devices, less of the traffic to the site will be coming from traditional
means like desktops and laptops.

### Account Recovery

With the solution in its current state, if a user forgets their login details
there is no way for them to recover or change them. As mentioned above, this
solution is only a prototype of the final site, and implementing this
functionality wasn't as important as implementing the more core features.

Implementing this feature could be done by creating a recovery page that allowed
you to enter the username of the lost account, then an email would be sent to
the email address stored in the database with a way to reset the password.

## Maintenance

Throughout my code I made good use of code comments that will be useful to both
be and anyone else reading the source code. This would help with maintenance as
it means that people reading the code are able to get a better understanding of
it. Also, this report itself serves as good documentation for why the system is
designed in the way it is and how everything that is used in it works.

In the design section of this report where I explain my test plan I talk about
automated testing. The plan was to implement automated testing for Corum. As
mentioned in that section, it would have made it much easier to make changes,
add features, and refactor code because the tests would be run automatically
whenever a change was made. For example, if I made a change that broke
something, the test suite would fail and it would give me a helpful error
message telling me what tests didn't pass.

Unfortunately I was not able to implement these automated tests. This would have
made the implementation stage of Corum much easier as it would have reduced the
amount of time I had to spend testing the system manually after each iteration.
The reason I didn't implement these tests is because Nuxt, the client framework
I am using, makes it hard to build robust tests at the moment. In future version
of the framework they have said that they will address this issue, and it will
be easier to create robust tests. This means that when this new version comes
out, it would be smart to add these tests to make the project more maintainable.

## Possible Improvements

If I were to spend more time developing Corum, I would likely implement the
following features:

### Migrate the API to a Newer Graphcool Version

The version of Graphcool that Corum's API uses is not the latest version. The
latest version removes most of the functionality that it provides such as
permissions, hook functions and resolvers. This is because other libraries
already provide ways to do this, so the Graphcool team decided to change the
direction of the project so that the service is now just a thin layer on top of
a MySQL database that allows the database to be queried using GraphQL. This
simplification of the service makes it a lot simpler to work with and it also
means that well established libraries can now be used in conjunction with it.

The issue is that migrating to the new version would require almost a complete
rewrite of Corum's API. This wouldn't be wise as this version of the site is
only a prototype of the final site. When the final site is created it would be
smart to build the API using this new version.

### Add GraphQL Subscriptions

GraphQL subscriptions provide a way for the client to create a connection to the
API server and 'subscribe' to a GraphQL query. When a client subscribes to a
query it means that if that data on the server changes, the client is
automatically sent the new data. This makes it relatively easier to implement
real time functionality in a client application.

Here are a few cases of where this would be useful in the context of Corum:

#### New Posts

On the subforum page, when a new post is created by a user, a link to it is
automatically added to the post list without the user reloading the page.

#### Live Vote Count

The vote count on both the subforum page and the post page could be made live.
This means that the number could be seen updating in real time.

#### New Comments

When a comment is added to a post and another user is on the same post, the
other user will automatically get the comment added to the page without
reloading the page.
