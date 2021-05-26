# Chapter 3. Creating Your First App

In this part, you will be creating a GraphQL API that interacts
with a DynamoDB NoSQL database to perform CRUD+L (create,
read, update, delete, and list) operations. You’ll learn what GraphQL
is, why developers are adopting it, and how it works.

We will be building a notes app that will allow users to create,
update, and delete notes. It will also have GraphQL subscriptions
enabled in order to see updates in real time. If another user is
interacting with the app and they create a new note, our app will
update with the new values in real time.

## Introduction to GraphQL

GraphQL is an API implementation that is an alternative to REST.
Let’s have a look at what GraphQL is, what a GraphQL API consists
of, and how GraphQL works.

### What Is GraphQL?

GraphQL is an API specification. It is a query language for APIs and
a runtime for fulfilling those queries with your data. It is, and can be
used as, a replacement for REST and has some similarities to REST.
GraphQL was introduced by Facebook in 2015, though it had been
used internally since 2012. GraphQL allows clients to define the
structure of the data that is required from an API call so that they can
know exactly what data structure is going to be returned from the
server. Requesting data in this way enables a much more efficient
way for client-side applications to interact with backend APIs and
services, reducing the amount of under-fetching of data, preventing
the over-fetching of data, and preventing type errors.


### What Makes Up a GraphQL API?
A GraphQL API consists of three main parts: schema, resolvers, and
data sources


![10](https://user-images.githubusercontent.com/23625821/119619757-98f0da00-be04-11eb-9ab8-4d2d99824a13.png)


The schema, written in GraphQL Schema Definition Language
(SDL), defines the data model (types) and operations that can beexecuted against the API. The schema consists of base types (data
models) and GraphQL operations like queries for fetching data;
mutations for creating, updating, and deleting data; and subscriptions
for subscribing to changes in data in real time.
Here is an example of a GraphQL schema:

```javascript 
 # base type
  type Todo {
  id: ID
  name: String
  completed: Boolean
  }
  
  # Query definitions
  type Query {
  getTodo(id: ID): Todo
  listTodos: [Todo]
  }
  
  # Mutation definitions
  type Mutation {
  createTodo(input: Todo): Todo
  }
  
  # Subscription definitions
  type Subscription {
  onCreateTodo: Todo
  }

```

Once the schema has been created, you can begin writing resolvers
for the GraphQL operations defined in the schema (query, mutation,
subscription). GraphQL resolvers tell the GraphQL operations what
to do when being executed and will typically interact with some data
source or another API


 ![1](https://user-images.githubusercontent.com/23625821/119620472-4fed5580-be05-11eb-9984-370d5d21996d.png)



### GraphQL Operations
GraphQL operations are how you interact with the API data sources.
GraphQL operations can be similarly mapped to HTTP methods for
RESTful APIs:

```javascript 
  GET -> Query
  PUT -> Mutation
  POST -> Mutation
  DELETE -> Mutation
  PATCH -> Mutation
```

A GraphQL request operation looks similar to a JavaScript object
with only the keys and no values. The keys and values are returned in
the GraphQL operation response. Here’s an example of a typical
GraphQL query fetching an array of items:

```javascript 
  query {
   listTodos {
     id
     name
     completed
    }
   }

```


This request would return the following response:

```javascript 
{
"data": {
"listTodos": [
   { "id": "0", "name": "buy groceries", "completed": false },
   { "id": "1", "name": "exercise", "completed": true }
  ]
 }
}
```

You can also pass arguments into a GraphQL operation. The
following operation is a query for a Todo, passing in the ID of the
Todo we’d like to fetch:

```javascript
query {
   getTodo(id: "0") {
     name
     completed
   }
}
```

This request would return the following response 

```javascript 
{
"data": {
"getTodo": {
   "name": "buy groceries"
   "completed": false
  }
 }
}
```

Though there are many ways to implement a GraphQL server, in this
part we will be using AWS AppSync. AppSync is a managed service
that allows us to deploy a GraphQL API, resolvers, and data sources
quickly and easily using the Amplify CLI.


## Creating the GraphQL API

Now that you have a basic understanding of what GraphQL is, let’s
go ahead and start using it to build the Notes app.

The first thing you need to do is create a new React application and
install the necessary dependencies. This app will be using the AWS
Amplify library to interact with the API, uuid for creating unique
ids, and the Ant Design library for styling:

```javascript
~ npx create-react-app notesapp
~ cd notesapp
~ npm install aws-amplify antd uuid
```

Now, within the root of the new app, you can create the Amplify
project:

```javascript 
~ amplify init
```

With the Amplify project initialized, we can then add the GraphQL
API:

```javascript
~ amplify add api

? Please select from one of the below mentioned services:
GraphQL
? Provide API name: notesapi
? Choose the default authorization type for the API: API Key
? Enter a description for the API key: public (or some
description)
? After how many days from now the API key should expire:
365 (or your
preferred expiration)
? Do you want to configure advanced settings for the GraphQL
API: N
? Do you have an annotated GraphQL schema? N
? Do you want a guided schema creation? Y
? What best describes your project: Single object with
fields
? Do you want to edit the schema now? Y

```

Next, open the base GraphQL schema (generated by the CLI), located
at notesapp/amplify/backend/api/notesapi/schema.graphql, in your
text editor. Update the schema to the following, and save it:

```javascript
type Note @model {
   id: ID!
   clientId: ID
   name: String!
   description: String
   completed: Boolean
}

```

This schema has a main Note type containing five fields. A field can
be either nullable (not required) or non-nullable (required). A non-
nullable field is specified with a ! character.


The Note type in this schema is annotated with an @model
directive. This directive is not part of the GraphQL SDL; instead, it is
part of the AWS Amplify GraphQL Transform library.


The GraphQL Transform library allows you to annotate a GraphQL
schema with different directives like @model, @connection,
@auth, and others.


The @model directive we used in this schema will transform the
base Note type into an expanded AWS AppSync GraphQL API
complete with:

