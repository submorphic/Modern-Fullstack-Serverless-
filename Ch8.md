
# Chapter 8, AWS AppSync In-Depth

## Building Skills for GraphQL, AppSync API, and React Router 

In this section, we’ll cover how to model relationships between GraphQL types, how to implement authorization rules on GraphQL types and fields, how to enable multiple authorization modes for an AppSync API, and how to enable route parameters using React Router.

### Relationships Between GraphQL Types

When creating a GraphQL API, or any API, modeling relationships between data becomes very important to understand.

For example, the app that we are building will have the following two types:


#### Stage
This type will hold the stage information for individual
performances, including the stage name and stage ID. Each stage
will have a number of performances that are associated with it.


#### Performance
This type will hold the individual performance information,
including the performer, the description, the stage of the
performance, and the time of the performance.

For this type of API, ideally you would want to have at least the following access patterns:

Query for a single stage and performances for the stage

Query for all stages and performances for each stage

Query for an individual performance and the corresponding stage info

Query for all performances and the corresponding stage info

The question is now usually this: how can you enable these different
relationships and access patterns? And in our case, how can we do
this using a NoSQL database like DynamoDB? There are two ways to
accomplish this:

Pattern your data in DynamoDB in a way that enables all of
these access patterns to be performed using a single table by
taking advantage of a combination of primary keys, sort
keys, and local secondary indexes. For this to work with
AppSync, we would have to write and maintain all of the
resolver logic by hand and from scratch.

Enable these relationships directly at the resolver level.
Because we are using GraphQL, and GraphQL enables per-
field resolvers, this can be done. To understand this better
let’s take a look at one of the types we will be working with.


### STAGE TYPE IN GRAPHQL

To better understand these concepts, let’s take a look at one of the
types we will be working with:

```grapghql
type Stage {
id: ID!
name: String!
performances: [Performance]
}
```
When creating a resolver, or resolvers, for this type, here is an
example chain of actions that you could assume would happen when
a request is made for stages and corresponding performances:

1. The main Stage GraphQL resolver will use the stage ID to
retrieve the stage information from the Stage table in the
database.

2. The field of performances on the Stage type will have
its own GraphQL resolver. This resolver should use the stage
ID to retrieve the related performances by querying the
database using a GSI, returning only the performances for
that stage ID.


### Custom Data Access Patterns Using GSIs

One of the most powerful things about DynamoDB is that it allows
(at the time of this writing) 20 additional GSIs per table.

Using either a GSI or a combination of GSI + sort key (also think of this as a filter key), you are able to create extremely flexible and powerful data access patterns for your data.

The GraphQL Transform library alsohas a directive, @key, that makes it simple to configure custom index
structures for @model types.


We’ll use the @key directive to create an access pattern that will
allow us to query performances for a given stage ID by setting the
stage ID as the GSI on the Performance table. Doing this will
allow us to be able to request stages and their corresponding
performances in a single GraphQL query.


### Starting to Build the App

To get started, we’ll again be walking through the steps of creating a
new React project, installing dependencies, initializing a new
Amplify app, and adding features via the CLI.
Change into the directory where you would like the app to live, and
create a new React project:
```
  ~ npx create-react-app festivalapp
  ~ cd festivalapp
```

Next, install the dependencies:
```
  ~ npm install aws-amplify antd @aws-amplify/ui-react react-router-dom
```

## Creating the Amplify App and Adding the Features

Next, initialize a new Amplify project in the root of the project directory:
```
~ amplify init
# Follow the steps to give the project a name, environment
name, and set the
default text editor.
# Accept defaults for everything else and choose your AWS
Profile.
```

Now, the Amplify project has been initialized and we can go ahead and start adding features.

## Building the Backend

The first feature we will add is authentication. This app will need to have basic authentication but will also need to have the ability to add admin users dynamically via a Lambda post-confirmation trigger.

To enable this, we will create the authentication service as well as a Lambda trigger that will allow us to add a predefined set of users into an Admin group as they sign up.

### Authentication

To add authentication with Cognito, we’ll again use the auth
category:

```
~ amplify add auth
? Do you want to use the default authentication and security
configuration?
Default configuration
? How do you want users to be able to sign in? Username
? Do you want to configure advanced settings? Yes? What attributes are required for signing up? Email
? Do you want to enable any of the following capabilities?
Add User to Group
? Enter the name of the group to which users will be added.
Admin
? Do you want to edit your add-to-group function now? Y
```

## Starting to Build the App

To get started, we’ll again be walking through the steps of creating a
new React project, installing dependencies, initializing a new
Amplify app, and adding features via the CLI.

Change into the directory where you would like the app to live, and
create a new React project:

```
 ~ npx create-react-app festivalapp
 ~ cd festivalapp
```

Next, install the dependencies:

```
 ~ npm install aws-amplify antd @aws-amplify/ui-react react-router-dom
 ```
 
 
## Creating the Amplify App and Adding the Features

Next, initialize a new Amplify project in the root of the project
directory:

```
 ~ amplify init
 # Follow the steps to give the project a name, environment name, and set the default text editor.
 # Accept defaults for everything else and choose your AWS Profile.

```

## Building the Backend

The first feature we will add is authentication. This app will need to have basic authentication but will also need to have the ability to add admin users dynamically via a Lambda post-confirmation trigger.

To enable this, we will create the authentication service as well as a Lambda trigger that will allow us to add a predefined set of users into an Admin group as they sign up.

### Authentication
To add authentication with Cognito, we’ll again use the auth
category:
```
  ~ amplify add auth
  ? Do you want to use the default authentication and security
  configuration?
  Default configuration
  ? How do you want users to be able to sign in? Username
  ? Do you want to configure advanced settings? Yes? What attributes are required for signing up? Email
  ? Do you want to enable any of the following capabilities?
  Add User to Group
  ? Enter the name of the group to which users will be added.
  Admin
  ? Do you want to edit your add-to-group function now? Y
```

Update the function with the following code and configure the
adminEmails array:

```javascript

// amplify/backend/function/<function_name>/src/add-to-group.js

const aws = require('aws-sdk');

exports.handler = async (event, context, callback) => {
  const cognitoProvider = new
  aws.CognitoIdentityServiceProvider({
  apiVersion: '2016-04-18'
  });
 let isAdmin = false
 /* set your admin emails here */
 const adminEmails = ['user1@somedomain.com', 'user2@somedomain.com']; 
 
 // If the user is one of the admins, set the isAdmin variable to true
 if (adminEmails.indexOf(event.request.userAttributes.email) !== -1) {
  isAdmin = true
 }
 
 const groupParams = {
  UserPoolId: event.userPoolId,
 }
 
 const userParams = {
   UserPoolId: event.userPoolId,
   Username: event.userName,
 }
 
 if (isAdmin) {
   groupParams.GroupName = 'Admin',userParams.GroupName = 'Admin'
  // First check to see if the groups exists, and if not create the group
  try {
    await cognitoProvider.getGroup(groupParams).promise();
  } catch (e) {
    await cognitoProvider.createGroup(groupParams).promise();
  }
   // If the user is an administrator, place them in the Admin group
  try {
    await cognitoProvider.adminAddUserToGroup(userParams).promise();
    callback(null, event);
  } catch (e) {
    callback(e);
  }
  } else {
    // If the user is in neither group, proceed with no action
    callback(null, event)
  }
}
```

### The AppSync API

Next, we’ll create the AppSync GraphQL API. Remember that for
this API, we will need to enable multiple authentication types for
both public and protected access. This can all be enabled by the CLI.
To add the AppSync API, we’ll use the api category:

```
~ amplify add api
? Please select from one of the below mentioned services:GraphQL
? Provide API name: festivalapi
? Choose an authorization type for the API: Amazon Cognito
User Pool
Do you want to configure advanced settings for the GraphQL
API: Yes
? Configure additional auth types? Y
? Choose the additional authorization types you want to
configure for the API:
API key
? Enter a description for the API key: public (or a custom
description)
? After how many days from now the API key should expire:
365 (or a custom
expiration date)
? Configure conflict detection? N
? Do you have an annotated GraphQL schema? N
? Do you want a guided schema creation? Y
? What best describes your project: Single object with
fields
? Do you want to edit the schema now? Y

```
This should open the GraphQL schema, located at ```amplify/backend/api/festivalapi/schema.graphql```, in your text editor.


The schema we will be using has two main types, a Stage and a Performance.

Use the following schema and continue:

```graphql

type Stage @model
@auth(rules: [
{ allow: public, operations: [read] },
{ allow: groups, groups: ["Admin"] }
]) {
  id: ID!
  name: String!
  performances: [Performance] @connection(keyName: "byStageId", fields: ["id"])
}


type Performance @model
@key(name: "byStageId", fields: ["performanceStageId"])
@auth(rules: [{ allow: public, operations: [read] },
{ allow: groups, groups: ["Admin"] }
]) {

  id: ID!
  performanceStageId: ID!
  productID: ID
  performer: String!
  imageUrl: String
  description: String!
  time: String
  stage: Stage @connection
}
```

#### @AUTH

First, the @auth directive allows us to pass in an array of
authorization rules. Each rule has an allow field (required) as well
as other metadata (optional), including things like specifying the
provider if it is different than the default authorization type.
In the Stage and Performance type, we’ve used two
authorization types, one for group access (groups) and another for
public access (public). You’ll notice that for the public access,
we’ve also set an array of operations. This array should contain a list
of the operations we would like to enable on the API. If there are no
operations listed, then by default all operations would be enabled.


#### @KEY
The @key directive enables us to add GSIs and sort keys to a
DynamoDB table for custom data access patterns. In the preceding
schema, we’ve created a key called byStageId that will allow us
to query the Performance table for performances by stage ID using a
field called performanceStageId (on the Performance
table). The resolver for the performances field will then use the
ID of the stage to query for performances by stage ID.


#### @CONNECTION
The @connection directive allows us to model relationships
between types. Types of relationships that can be created are belongs
to, one to many, many to one, or many to many.

