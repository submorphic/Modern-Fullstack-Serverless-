
# Chapter 2,  Getting Started with AWS Amplify

At the core of most applications is the data/API layer. This layer
could look like many things. In the serverless world, this usually will
be composed of a combination of API endpoints and serverless
functions. These serverless functions could be doing some logic and
returning data, interacting with a database of some kind, or even
interacting with another API endpoint.

There are two main ways of creating APIs with Amplify:
1. A combination of Amazon API Gateway and a Lambda
function

2. A GraphQL API connected to some type of data source
(database, Lambda function, or HTTP endpoint)

API Gateway is an AWS service that allows you to create API
endpoints and route them to different services, often via a Lambda
function. When you make an API call, it will route the request
through API Gateway, invoke the function, and return the response.
Using the Amplify CLI, you can create both the API Gateway
endpoint as well as the Lambda function; the CLI will automatically
configure the API to be able to invoke the Lambda function via an
HTTP request.


![1](https://user-images.githubusercontent.com/23625821/119357985-6f22a080-bca8-11eb-9780-f84b5ac4cf0c.png)


## Creating and Deploying a Serverless Function

At the core of many serverless applications are serverless functions.
Serverless functions run your code in stateless compute containers
that are event-driven, short-lived (may last for one invocation), and fully managed by the cloud provider of your choice. These functions
scale seamlessly and do not require any server operations.
While most people think of serverless functions as being invoked or
triggered by an API call, these functions can also be triggered by a
variety of different events. In addition to HTTP requests, a few
popular ways to invoke a serverless function are via an image upload
to a storage service, a database operation (like create, update, or
delete), or even from another serverless function.
Serverless functions scale automatically, so there’s no need to worry
about your application if you get a large spike in traffic. The first time
you invoke a function, the service provider will create an instance of
the function and run its handler method to process the event. After the
function finishes and returns a response, it will remain and process
additional events if they come in. If another invocation happens while
the first event is still processing, the service will then create another
instance.
Serverless functions also have a payment model that is different from
traditional infrastructure. With services like AWS Lambda, you only
pay for what you use and are charged based on the number of
requests for your functions and the time it takes for your code to
execute. This is in contrast to provisioning and paying for
infrastructure like servers regardless of whether they are being
utilized.
Now that you know about serverless functions, let’s take a look at
how you can create a serverless function and hook it up to an API that will invoke it from an HTTP request.


## Creating the React Application and Installing the Dependencies

To get started, you’ll first need to create the React application. To do
so, you can use npx:


```javascript 
  ~ npx create-react-app amplify-react-app
  ~ cd amplify-react-app
```


