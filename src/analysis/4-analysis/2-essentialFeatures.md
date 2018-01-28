# Essential Features

From my research into existing systems, as well as the interviews I have done, I
believe this to be a good set of basic features that Corum needs.

## Page Layout

* Header (Top of the page)
  * Top left - Logo linking to homepage
  * Top Right - Login/logout/sign-up button/link
* Navigation (Left side under Header, think this is better placement than Reddit
  at the top as it is easier to navigate through)
  * Sub-forum subscriptions at the top (Called Favorites, users can access their
    favorites first. This will mean that returning users can access content they
    want to see quicker)
  * All other sub-forums below (Search bar of all sub-forums for easier
    navigation around the site)
  * Highlight the currently selected sub-forum in the nav (So the user can
    easily see where they are)
* Current sub-forum (Render selection message upon first load/after login when
  no sub-forum is selected - gives first time user some helpful instructions on
  what to do)
  * Give user sort selection (newest / most popular [This gives the user a
    choice on how they wish to view the content])
  * Eventually make it so posts are loaded in dynamically (paginated)
  * Each post
    * Current amount of up votes (Shows popularity of post, which could be an
      indication of how good the post is and if it is worth viewing)
    * Title -> links to link/post
* Selected post (Fills the space where the sub-forum was)
  * Title (So that the user can always see where they are)
  * Time Posted (This can give context of how relevant the content is [For
    example, an older post may not be so up to date])
  * User that posted it (Could tell the user if the information is credible)
  * Amount of votes
  * Content
  * Comments (For discussion of the post, show message if empty)

## General Features

* Login System
  * Account Creation / Sign-up (username, password)
  * Require login to post/comment (So that other users know who posted / said
    what)
* Self governing voting system
  * Each user gets a single vote (up or down) for each post (like Reddit)
  * Posts with positive votes rise to the top (popularity sort)
  * Posts with negative votes go to the bottom (popularity sort)
  * Posts that get -25 (due to change) 'votes' are removed from the sub-forum
    * This point is the key to the self governing system, as the features before
      it are borrowed from Reddit.
    * Theoretically, no matter how popular the sub-forum is, if more people like
      the post that don't, the post will remain.
    * If more people dislike a post than like it (within the vote threshold)
      then it will be removed automatically.
    * This means, that instead of a moderator moderating the sub-forum, the
      users do it themselves. This comes back to the point for neutrality and
      fairness, as the sub-forum community as a whole gets to decide what is
      allowed on the sub-forum.
* API
  * Use GraphQL
  * Setup the backend on graphcool
