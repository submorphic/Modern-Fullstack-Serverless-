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

To understand the events coming into Lambda from the event
sources, it’s important to underscore the following point: the shape of
the event data will differ between different event types. For instance,
the HTTP event data structure coming from API Gateway will be
different than the Amazon S3 event data structure, and the Amazon
S3 event data structure will differ from the Amazon Cognito data
structure.


### API Gateway Event

The API gateway event data is the data structure that will be passed
into the Lambda function when invoking it from an API Gateway
HTTP event, like GET, PUT, POST, or DELETE. This data structure
holds information like the HTTP method that invoked the function,
the path that was invoked, the body if one was passed in, and the
identity of the user calling the API (inside the
requestContext.identity field) if the user was
authenticated:

```javascript 
{
    "resource": "/items",
    "path": "/items",
    "httpMethod": "GET",
    "headers": { /* header info */ },
    "multiValueHeaders": { /* multi value header info */ },
    "queryStringParameters": null,
    "multiValueQueryStringParameters": null,
    "pathParameters": null,
    "stageVariables": null,
    "requestContext": {
    "resourceId": "b16tgj",
    "resourcePath": "/items",
    "httpMethod": "GET",
    "extendedRequestId": "CzuJMEDMoAMF_MQ=","requestTime": "07/Nov/2019:21:46:09 +0000",
    "path": "/dev/items",
    "accountId": "557458351015",
    "protocol": "HTTP/1.1",
    "stage": "dev",
    "domainPrefix": "eq4ttnl94k",
    "requestTimeEpoch": 1573163169162,
    "requestId": "1ac70afe-d366-4a52-9329-5fcbcc3809d8",
    "identity": {
    "cognitoIdentityPoolId": "",
    "accountId": "",
    "cognitoIdentityId": "",
    "caller": "",
    "apiKey": "",
    "sourceIp": "192.168.100.1",
    "cognitoAuthenticationType": "",
    "cognitoAuthenticationProvider": "",
    "userArn": "",
    "userAgent": "Mozilla/5.0 (Macintosh; Intel Mac OS
    X 10_11_6)
    AppleWebKit/537.36 (KHTML, like Gecko)
    Chrome/52.0.2743.82
    Safari/537.36 OPR/39.0.2256.48",
    "user": ""
    },
    "domainName": "eq4ttnl94k.execute-api.us-east-
    1.amazonaws.com",
    "apiId": "eq4ttnl94k"
    },
    "body": null,
    "isBase64Encoded": false
}


```
