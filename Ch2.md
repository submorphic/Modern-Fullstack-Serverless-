
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

Next, you will need to install the dependencies. For this app, you’ll
only need the AWS Amplify library:

```javascript
  ~ npm install aws-amplify
```

After installing the dependencies, you can now initialize a new
Amplify project in the root of the React application:


```javascript 
~ amplify init
? Enter a name for the project: cryptoapp
? Enter a name for the environment: local
? Choose your default editor: <your-preferred-editor>
? Choose the type of app that you're building: javascript
? What javascript framework are you using: react
? Source Directory Path: src
? Distribution Directory Path: build
? Build Command: npm run-script build
? Start Command: npm run-script start
? Do you want to use an AWS profile? Here, choose *Y* and
pick the AWS
profile you created when you ran `amplify configure`.

```

Now, both the Amplify project and the React app have been
successfully created and you can begin adding new features.


## Creating a New Serverless Function with the
Amplify CLI
In the next step, we’ll create the serverless function that you will be
using for this app. The app you are building in this chapter is a
cryptocurrency app. At first, you will hardcode an array of
cryptocurrency information in the function and return it to the client.
Later in this chapter, you’ll update this function to call another API
(CoinLore) and asynchronously fetch and return data.
To create the function, run the following command:

```javascript 
  ~ amplify add function
  ? Select which capability you want to add: Lambda function
  ? Provide a friendly name for your resource to be used as a
  label for
  this category in the project: cryptofunction
  ? Provide the AWS Lambda function name: cryptofunction
  ? Choose the function runtime that you want to use: NodeJS
  ? Choose the function template that you want to use:
  Serverless express
  function (Integration with Amazon API Gateway)
  ? Do you want to access other resources created in this
  project from
  your Lambda function? No
  ? Do you want to invoke this function on a recurring
  schedule? No
  ? Do you want to configure Lambda layers for this function?
  No
  ? Do you want to edit the local Lambda function now? No
``` 

You should now see a new subfolder located within the amplify
directory at ``` amplify/backend/function/cryptofunction ``` 


## Walking Through the Code

When you created this resource, a new folder in amplify/backend was
created named function. All of the functions created by the CLI will
be stored in this folder. For now, you only have a single function,
cryptofunction. In the cryptofunction folder, you will see a
couple of configuration files as well as an src directory where the
main function code is located.

Serverless functions are essentially just encapsulated applications
running on their own. Because the function you created is in
JavaScript, you’ll see that there are all of the things you’d typically
see in any JavaScript application, including package.json and index.js
files.

Next, have a look at the function entry point located at src/index.js, in
the cryptofunction folder. In this file you’ll see that there is a function
called exports.handler. This is the entry point for the function
invocation. When the function is invoked, this is the code that is run.


You can handle the event directly in this function if you would like,
but since you will be working with an API, a more useful way to do
this is to proxy the path into an express app with routing (i.e.,
http://yourapi/<somepath>). Doing this gives you multiple routes in
a single function as well as multiple HTTP request methods like get,
put, post, and delete for each route. The serverless expressframework provides an easy way to do this and has been built into the
function boilerplate for you.
  
In index.js, you will see a line of code that looks like this:
  
```javascript   
  awsServerlessExpress.proxy(server, event, context);  
```
  
This code is where the event, context, and path are proxied to the
express server running in app.js.
In app.js, you will then be able to create HTTP requests against
whatever routes you create for your API (this example being a
/coins route to fetch cryptocurrency).

