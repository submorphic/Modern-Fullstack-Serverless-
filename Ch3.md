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
