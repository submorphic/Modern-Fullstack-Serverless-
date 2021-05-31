# Chapter 6,  Serverless Functions In-Depth


In previous parts, you learned how to create and interact with a serverless
API using API Gateway and AWS Lambda. Here, you’ll continue to
learn about how to use serverless functions by creating two new types
of functions. The functions in this part will be different in that,
instead of using them as strictly a web server or an API, you’ll be
using them to interact with other AWS services to aid in the
application-development process.


You’ll be creating the following two kinds of functions in this
part:

#### A function that dynamically adds a user to a group based on their email address

In some applications, you will need to perform “coarse-grained”
access control, which typically means granting certain
permissions to users in a broad way based on the type of role or
group they are associated with. In our example, we’ll have an
administrator group of users that will be identified by their email
address. If a user signs up with one of these email addresses, we
will place them in a group called Admin.

#### A function that automatically resizes an image after it has been uploaded to Amazon S3

Many applications require dynamic image resizing on the server
after a user has uploaded an image. This is done for many
reasons, ranging from the need to make the web application more
performant by compressing images to the need to dynamically
create avatars or thumbnail images of a smaller size for images.

## Event Sources and Data Structure

In part 2, we briefly talked about event sources for serverless functions as part of an event-based architecture.

The only event source we have implemented up until this point has been from API Gateway: an HTTP request that triggered the function, and fetched
data from an API and returned it in the response.

In this part, we’ll be working with two other event types and sources, one from Amazon S3 and one from Amazon Cognito. 



