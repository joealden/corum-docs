# Navigation

This component will also have 2 different states like the Header component.

When the user is not logged in, only the `All sub-forums` section will render.
(As shown [here](#login-page)) This `All sub-forums` component will show all of
the sub-forums that Corum has with a search bar at the top. If the sub-forum
list is longer than the components height, it will scroll independently of the
page, with the search bar staying at the top of the navigation.

When the user is logged in, a `favorites` section will also be rendered above
the `All sub-forums` section. (As shown [here](#new-post)) The `favorites`
section will list the sub-forums that the user has added to their `favorites`. A
user can add a sub-forum to their favorites by clicking on the `+` icon next to
the sub-forum in the 'All sub-forums' section. The `+` icon is only rendered if
the user is logged in and the subforum is not already in their favorites. A user
can remove a sub-forum from their favorites by clicking the `-` sign next to the
sub-forum they wish to remove. The `favorites` section will be very similar to
the `All sub-forums` section, however it will not have a search bar.

Both the `favorites` and the `All sub-forums` section will be sorted
alphabetically so that it is easier to find a subforum.

Each sub-forum in both sections will be a Link component. They will link to a
sub-forum in the pattern `'/subforum/:subforum'`. (For example,
`'/subforum/programming'`)
