# Error Page

In development, these error pages will give detailed error messages for
debugging purposes, however in production, showing these details error messages
may be of use to malicious users. For this reason, when in production mode,
Corum will show helpful messages for the following situations:

* They attempt to visit a subforum that doesn't exist
* They attempt to visit a post that doesn't exist
* They attempt to visit any other route that doesn't exist

If an unhandled error occurs, a generic error will be displayed to not give any
information to a malicious users that could result in an exploit.
