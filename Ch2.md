
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

