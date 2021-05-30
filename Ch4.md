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

### How Amazon Cognito Works
Cognito has two main pieces: user pools and identity pools:

#### User pools

These provide a secure user directory that stores all your users
and scales to hundreds of millions of users. It is a fully managed
service. As a serverless technology, user pools are easy to set up
without having to worry about standing up any infrastructure.
User pools are what manage all of the users that sign up and sign
in to the account, and are what we will be focusing on in this
chapter.

#### Identity pools

These allow you to authorize users that are signed in to your
application to access various other AWS services. Say you wanted
to allow a user to have access to a Lambda function so that they
could fetch data from another API; you could specify that while
creating an identity pool. Where user pools come in is that the
source of these identities could be a Cognito user pool or even
Facebook or Google.

