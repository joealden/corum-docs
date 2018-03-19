# Signup Page

These also show the following:

* The url can be seen at the top (`/signup`)
* The Sign Up button in the header is highlighted correctly
* On submit, the sign up button is no clickable and says 'Please Wait...'

## Successful Signup Attempt

* The user is redirected to the page they were on before if successful
* The logged in state has been changed (Show in navigation and header)
* It is assumed that the email and username are not already taken

Location: **`images/gifs/signup/correct.gif`**

![Successful Signup Attempt GIF](images/gifs/signup/correct.gif)

## Input validation

* Mention that the validation code can be found below (maybe link)
* The Sign Up button can only be pressed when:
  * All fields have been filled
  * The two passwords fields match
* In this GIF, it can be seen that the passwords do not match, as they of
  different length

Location: **`images/gifs/signup/input-validation.gif`**

![Input validation GIF](images/gifs/signup/input-validation.gif)

## Email is Already Taken

* The correct error message is displayed
* It is assumed that the email `test@test.com` is already taken
* It is assumed that the username `test12345` is not taken

Location: **`images/gifs/signup/email-taken.gif`**

![Username taken GIF](images/gifs/signup/email-taken.gif)

## Username is Already Taken

* The correct error message is displayed
* It is assumed that the username `test` is already taken
* It is assumed that the email `test123@test.com` is not taken

Location: **`images/gifs/signup/username-taken.gif`**

![Username taken GIF](images/gifs/signup/username-taken.gif)

## Email and Username are Already Taken

* The correct error message is displayed
* It is assumed that the email `test@test.com` is already taken
* It is assumed that the username `test` is already taken

Location: **`images/gifs/signup/both-taken.gif`**

![Both taken GIF](images/gifs/signup/both-taken.gif)

## Navigating to the Login Page

* The link provided on the signup page to the login page works

Location: **`images/gifs/signup/login-link.gif`**

![Successful Login Attempt GIF](images/gifs/signup/login-link.gif)
