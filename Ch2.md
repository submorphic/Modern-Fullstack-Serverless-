
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


## Creating the /coins Route
  
Now that you have seen how the application is structured, let’s create
a new route in app.js and return some data from it. The route that you
will be creating is a /coins route. This route will be returning an
object containing a coins array.
  
Let’s add the new route. Before the first app.get('/items')
route, add the following code:
  
```javascript 
  
/* amplify/backend/function/cryptofunction/src/app.js /*
  
app.get('/coins', function(req, res) {
  const coins = [
    { name: 'Bitcoin', symbol: 'BTC', price_usd: "10000" },
    { name: 'Ethereum', symbol: 'ETH', price_usd: "400" },
    { name: 'Litecoin', symbol: 'LTC', price_usd: "150" }
  ]
  res.json({coins}); 
});  
```
 
## Adding the API

Now that the function is created and configured, let’s put an API in
front of it so you can trigger it with an HTTP request.
To do this, you will be using Amazon API Gateway. API Gateway is
a fully managed service that enables developers to create, publish,
maintain, monitor, and secure REST and WebSocket APIs. API
Gateway is one of the services supported by both the Amplify CLI as
well as the Amplify client library.

  
### Creating a New API
To create the API, you can use the Amplify add command. From the
root of the project, run the following command in your terminal:

```javascript 
  
  ~ amplify add api
  ? Please select from one of the below mentioned services:
  REST? Provide a friendly name for your resource to be used as a
  label for
  this category in the project: cryptoapi
  ? Provide a path: /coins
  ? Choose a Lambda source: Use a Lambda function already
  added in the
  current Amplify project
  ? Choose the Lambda function to invoke by this path:
  cryptofunction
  ? Restrict API access: N
  ? Do you want to add another path? N
  
```

### Deploying the API and the Lambda Function
  
Now that the function and API have both been created, you need to
deploy them to your account to make them live. To do so, you can run
the Amplify push command:
  

```javascript 
~ amplify push
? Are you sure you want to continue? Y
```
  
Once the deployment has successfully completed, the services are
live and ready to use.
  
You can use the Amplify CLI status command at any time to see
the current status of your project. The status command will list out
all of the currently configured services in your project and give you
the status for each of them:
  
```javascript 
  ~ amplify status
  Current Environment: local
  | Category | Resource name | Operation | Provider plugin
  |
  | -------- | -------------- | --------- | -----------------
  |
  | Function | cryptofunction | No Change | awscloudformation|
  | Api
  |
  | cryptoapi
  | No Change | awscloudformation  
```
  
The main thing to notice in this status output is the Operation. The
Operation tells you what will happen the next time push is run in
the project. The Operation property will be set to Create,
Update, Delete, or No Change.

  
## Interacting with the New API
Now that the resources have been deployed, you can begin interacting
with the API from the React application.
  
### Configuring the Client App to Work with Amplify
  
To use the Amplify client library in any application, there is a base
configuration that needs to be set up, usually at the root level. When
you create the resources, the CLI populates the aws-exports.js file
with information about your resources. You will use this file to
configure the client application to work with Amplify.
To configure the app, open src/index.js and add the following below
the last import:

```javascript 
    import Amplify from 'aws-amplify'
    import config from './aws-exports'
    Amplify.configure(config); 
 ```
  
### The Amplify Client API Category
After the client application has been configured, you can begin
interacting with your resources.
  
The Amplify client library has various API categories that can be
imported and used for various types of functionality, including Auth
for authentication, Storage for storing items in S3, and API for
interacting with REST and GraphQL APIs.
  
In this section, you will be working with the API category. API has
various methods available—including API.get, API.post,
API.put, and API.del—for interacting with REST APIs, and
API.graphql for interacting with GraphQL APIs.
  
When working with a REST API, API takes in three arguments:
API.get(apiName: String, path: String, data?: Object)

#### apiName
The name given when you create the API from the command line.
In our example, this value would be cryptoapi.

#### path
The path that you would like to interact with. In our example, we
created /coins, so the path would be /coins.

#### data
This is an optional object containing any properties you’d like to
pass to the API, including headers, query string parameters, or a
body.

  
In our example, the API call is going to look like this:

```javascript
  API.get('cryptoapi', '/coins');
```

  The API returns a promise, meaning you can handle the call using
either a promise or an async function:
 
  ```javascript
// promise
API.get('cryptoapi', '/coins')
.then(data => console.log(data))
.catch(error => console.log(error))

// async await
const data = await API.get('cryptoapi', '/coins')
```
  
In the examples in this book, we’ll be handling promises using
async functions.

  
### Calling the API and Rendering the Data in React
Next, let’s call the API and render the data. Update src/App.js with
the following:
  
```javascript 
 
  // Import useState and useEffect hooks from React
import React, { useState, useEffect } from 'react'
// Import the API category from AWS Amplify
import { API } from 'aws-amplify'
import './App.css';
function App() {
// Create coins variable and set to empty array
const [coins, updateCoins] = useState([])
// Define function to all API
async function fetchCoins() {
const data = await API.get('cryptoapi', '/coins')
updateCoins(data.coins)
}
 
  // Call fetchCoins function when component loads
useEffect(() => {
fetchCoins()
}, [])
return (
<div className="App">
{
coins.map((coin, index) => (
<div key={index}>
<h2>{coin.name} - {coin.symbol}</h2>
<h5>${coin.price_usd}</h5>
</div>
))
}
</div>
);
}
export default App; 
```

Then, run the app: 
  
```javascript
  ~ npm start 
```

  
## Updating the Function to Call Another API
  
Next, you’ll update the function to call another API, the CoinLore
API, that will return dynamic data from the CoinLore service. The
user will be able to add set filters like limit and start to limit the
number of items coming back from the API.
To get started, you will first need a way to interact with an HTTP
endpoint in the Lambda function. The library you will be using forthis lesson is the Axios library. Axios is a promise-based HTTP client
for the browser and Node.js
  
