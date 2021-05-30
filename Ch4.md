# Chapter 4,  Introduction to Authentication

Authentication and identity are integral parts of almost any
application. Knowing who the user is, what permissions they have,
whether or not they are signed in, and having a unique identifier for
the user allow your application to render the correct views and return
the proper data for the currently signed-in user.

Most applications require mechanisms to handle user sign-up, user
sign-in, password encryption, and updating, as well as countless other
tasks around identity management. Modern applications often call for
things like open authentication (OAUTH), multifactor authentication
(MFA), and time-based on time passwords (TOTP).



## Introduction to Amazon Cognito

Amazon Cognito is a fully managed identity service from AWS.
Cognito allows for simple and secure user sign-up, sign-in, access
control, and user identity management. Cognito scales to millions of
users and also supports sign-in with social identity providers, such as
Facebook, Google, and Amazon. It is also free for the first 50,000
users of any app.

