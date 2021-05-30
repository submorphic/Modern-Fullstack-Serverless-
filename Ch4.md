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
In the past, developers had to hand roll all of this authentication
functionality from scratch. This task alone could take a team of
developers weeks, or even months, to get right and to do so securely.
Today there are fully managed authentication services like Auth0,
Okta, and Amazon Cognito that handle all of this for us.
In this chapter, you will learn how to properly and securely
implement authentication in a React application using Amazon
Cognito with AWS Amplify.The app that you will be building is a basic application that requires
authentication in order to be viewed and also has a profile page
showing profile information about the signed-in user. If the user is
signed in, they can navigate between a public route, a profile route
with an authentication form, and a protected route viewable only to
signed-in users.
If they are not signed in, they can only view the public route and
authentication form on the profile route. If the user tries to access a
protected route when they are not signed in, we want to redirect them
to the authentication form so that they can sign in and then access the
route once authenticated.
This app will also persist user state, so if they refresh the app or
navigate away from it and come back, they will stay signed in.


