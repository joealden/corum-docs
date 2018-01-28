# Test Data

For some of the items listed about in the test plan, test data is required. In
the following subsections, the items that required test data are given relevant,
justified test data. The items that are not covered below cannot be given test
data, and they instead rely on testing using a web browser / 'end to end'
testing. (Explained later on)

## Creating a User

To properly test the account creation implementation, I will need to test what
happens when the following occurs:

* A valid, not already taken email and / or username are given
* A valid, already taken email is given
* An already taken username is given
* An invalid email is given

When testing the situation where a not already taken email or username are
given, I need to ensure beforehand that no entries exist with the used test
data, otherwise the test might fail when it shouldn't.

The following data will be used:

* A valid email - `test@test.com`
* Invalid emails - `test@`, `test`, `@test`, `@test.com`, `@`

This data ensures that edge cases like some of those above (For example, `test@`
or `@test.com`) are not allowed into the system.

## Logging in a User

To properly test the account authentication implementation, I will need to test
what happens when the following occurs:

* An existing email and correct password are given
* An existing email and incorrect password are given
* An email that doesn't exist is given

Just like when testing the creation of a user, I need to ensure beforehand that
when testing that giving the correct details works, the user exists on the
server. Likewise, I need to ensure that the user doesn't exist when testing
failing login attempts.

The following data will be used:

* An existing set of details:
  * Email - `exists@test.com`
  * Password - `exists123`
* An incorrect email - `nothing@test.com`
* An incorrect password - `nothing321`

## Creating a Post

To properly test the new post page, I will need to give it data that is varied
in length. This is because I need to test how the post page will handle titles
and content of different sizes.

The following data will be used:

* Short title - `testing title`
* Short content - `testing content`
* Long title -
  `This is quite a long title that will be used for testing purposes to verify the post page displays it correctly`
* Long content (From randomtextgenerator.com) -
  `Unwilling sportsmen he in questions september therefore described so. Attacks may set few believe moments was. Reasonably how possession shy way introduced age inquietude. Missed he engage no test of. Still tried means we aware order among on. Eldest father can design tastes did joy settle. Roused future he ye an marked. Arose mr rapid in so vexed words. Gay welcome led add lasting chiefly say looking. Is allowance instantly strangers applauded discourse so. Separate entrance welcomed sensible laughing why one moderate shy. We seeing piqued garden he. As in merry at forth least ye stood. And cold sons yet with. Delivered middleton therefore me at. Attachment companions man way excellence how her pianoforte. Oh to talking improve produce in limited offices fifteen an. Wicket branch to answer do we. Place are decay men hours tiled. If or of ye throwing friendly required. Marianne interest in exertion as. Offering my branched confined oh test. Dispatched entreaties boisterous say why stimulated. Certain forbade picture now prevent carried she get see sitting. Up twenty limits as months. Inhabit so perhaps of in to certain. Sex excuse chatty was seemed warmth. Nay add far few immediate sweetness earnestly dejection.`

## Creating a Comment

To properly test the creation of a comment, I will need to give it data that is
varied in length. This is because I need to test how the post page will handle
comments of different sizes.

The following data will be used:

* Short comment - `testing comment`
* Long comment (From randomtextgenerator.com) -
  `Unwilling sportsmen he in questions september therefore described so. Attacks may set few believe moments was. Reasonably how possession shy way introduced age inquietude. Missed he engage no test of. Still tried means we aware order among on. Eldest father can design tastes did joy settle. Roused future he ye an marked. Arose mr rapid in so vexed words. Gay welcome led add lasting chiefly say looking.`
