# Test Plan

To test that Corum is working as expected, the 'Success Criteria' list found in
the analysis section of the report needs to be met.

This included the following:

* The UI should resemble the layout seen in the
  [GUI Design section](#gui-design)
* Users can click on subforums, and the site displays the subforum's posts.
* Users can click on posts, and the site displays the content of the post.
* Users can click on the `signup` button and signup to the site (Details saved
  to backend)
* Users can click on the `login` / `logout` button and login to / logout of the
  site (Check details against the backend)
* Logged in users can add & remove subforums to their `favorites`
* Logged in users can vote on posts
* Logged in users can create new posts
* Logged in users can comment on posts

If all of these tests pass, then the site is verified to be in working order.
All of these tests should be done many time during the iterative development
process, and then once gain once development has finished.

I will write automated tests so that I can ensure the site works quickly.
