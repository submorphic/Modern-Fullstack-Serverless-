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

1. Additional schema definitions for queries and mutations
(Create, Read, Update, Delete, and List operations)
2. Additional schema definitions for GraphQL subscriptions
3. DynamoDB database
4. Resolver code for all GraphQL operations mapped to
DynamoDB database
To deploy the API, you can run the push command:

```javascript
 ~ amplify push
```

Once the deployment has completed, the API and database have
successfully been created in your account. Next, let’s open the newly
created AppSync API in the AWS Console and test out a few
GraphQL operations.


Viewing and Interacting with the
GraphQL API
To open the API in the AWS Console at any time, you can use the
following command:

```javascript
- amplify console api
> Choose GraphQL
```

Once you’ve opened the AppSync console, click Queries in the
lefthand menu to open the query editor. Here, you can test out
GraphQL queries, mutations, and subscriptions using your API.
The first operation we’ll try out is a mutation to create a new note. In
the query editor, execute the following mutation: 

```javascript
mutation createNote {
  createNote(input: {
     name: "Book flight"
     description: "Flying to Paris on June 1 returning June
     10"completed: false
     }) {
     id name description completed
  }
}
```

Now that you’ve created an item, you can try querying for it. Let’s try
to query for all of the notes in the app:

```javascript

query listNotes {
    listNotes {
       items {
         id
         name
         description
         completed
    }
   }
}

``` 

You can also try querying for a single note using the ID of one of the
notes:

```javascript

query getNote {
  getNote(id: "<NOTE_ID>") {
     id
     name
     description
     completed
  }
}
```


### Building the React Application

The first thing you will need to do is configure the React application
to recognize the Amplify resources located at src/aws-exports.js. To
do so, open src/index.js and add the following below the last import:

```javascript

import Amplify from 'aws-amplify'
import config from './aws-exports'
Amplify.configure(config)

```

#### Listing Notes (GraphQL Query)

Now that the application has been configured, you can begin making
calls against the GraphQL API. The first operation we will be
implementing will be a query to list all of the notes.


The query will return an array and we will map over all of the items
in the array, showing the note name, description, and whether or not it
is completed.

In src/App.js, first import the following at the top of the file:

```javascript 

import React, {useEffect, useReducer} from 'react'
import { API } from 'aws-amplify'
import { List } from 'antd'
import 'antd/dist/antd.css'
import { listNotes } from './graphql/queries'

```

Next, we will need to create a variable to hold our initial application
state. Because our application will be holding and working with
multiple state variables, we will use the useReducer hook from
React to manage state.
useReducer has the following API:

```javascript 
  const [state, dispatch] = useReducer(reducer <function>, initialState <any>); 

```

useReducer accepts a reducer function of type (state, action) => newState and initialState as arguments:

```javascript 

/* Example of some basic state */

  const initialState = { notes: [] }
  
  /* Example of a basic reducer */

  function reducer(state, action) {
     switch(action.type) {
     case 'SET_NOTES':
     return { ...state, notes: action.notes }
     default:
     return state
     }
  }

  /* Implementing useReducer */

  const [state, dispatch] = useReducer(reducer: <function>, initialState: <any>);
  
  /* Sending an update to the reducer */
  
  const notes = [{ name: 'Hello World' }]
  
  dispatch({ type: 'SET_NOTES', notes: notes })
  
  /* Using the state in your app */
  
  {
    state.notes.map(note => <p>{note.name}</p>)
  }
```

When invoked, the useReducer hook returns an array containing
two items:

1. The application state
A dispatch function (this function allows you to update
the application state)

2. The initial state of our Notes application will hold an array for the
notes, form values, error, and loading state.


In src/App.js, add the following initialState object after the last import:


```javascript 
 const initialState = {
   notes: [],
   loading: true,
   error: false,
   form: { name: '', description: '' }
 }
```

Then create the reducer. For now, the reducer will only have cases to
either set the notes array or set an error state:


```javascript 
function reducer(state, action) {
   switch(action.type) {
   case 'SET_NOTES':
   return { ...state, notes: action.notes, loading: false
   }
   case 'ERROR':
   return { ...state, loading: false, error: true }
   default:
   return state
   }
}
```

Next, update the main App function to create the state and dispatch
variables by calling useReducer and passing in the reducer and initialState:

```javascript 
 export default function App() {
    const [state, dispatch] = useReducer(reducer, initialState); 
 }
```

To fetch the notes, create a fetchNotes function (in the main App function) that will call the AppSync API and set the notes array once the API call is successful:

```javascript 
async function fetchNotes() {
   try {
     const notesData = await API.graphql({
     query: listNotes
   })
   
   dispatch({ type: 'SET_NOTES', notes: notesData.data.listNotes.items }); 
   
   } catch (err) {
     console.log('error: ', err)
     dispatch({ type: 'ERROR' })
   }
}
```

Now, invoke the fetchNotes function by implementing the
useEffect hook (in the main App function):

```javascript 
useEffect(() => {
  fetchNotes()
}, [])

```

The next thing you need to do is return the main UI for the
component. In the main App function, add the following:

```javascript
   return (
      <div style={styles.container}>
      <List
      loading={state.loading}
      dataSource={state.notes}
      renderItem={renderItem}
      />
      </div>
   )

```

Here we are using the List component from Ant Design. This
component will map over an array (dataSource) and return an
item for each item in the array by calling the renderItem function.

Next, define renderItem (in the main App function):

```javascipt
function renderItem(item) {
 return (
   <List.Item style={styles.item}>
   <List.Item.Meta
   title={item.name}
   description={item.description}
   />
   </List.Item>
 )
}
```

Finally, create the styles for the components we will be using for this app:

```javascript 
   const styles = {
      container: {padding: 20},
      input: {marginBottom: 10},
      item: { textAlign: 'left' },
      p: { color: '#1890ff' }
   }

```
Now we are ready to run the app! In the terminal, run the start command:

``` javascript
~ npm start

```

### Creating Notes (GraphQL Mutation)

Now that you know how to query for a list of notes, let’s take a look
at how to create a new note. To do so, you’ll need the following:

1. A form to create a new note
2. A function to update the state as the user types into the form
3. A function to add the new note to the UI and send an API call to create a new note


First, import the UUID library so you can create a unique identifier for the client.

We do this now so that later on when we implement subscriptions we can identify the client that created the note.

We will also import the Input and Button components from Ant Design:

```javascript
import { v4 as uuid } from 'uuid'
import { List, Input, Button } from 'antd'
```


Next, you will need to import the createNote mutation definition:

```javascript 
import { createNote } from './graphql/mutations'
```

Then, create a new CLIENT_ID variable below the last import:

```javascript 
const CLIENT_ID = uuid()
```

Update the switch statement in the reducer to add three new cases.
We will need a new case for the following three actions:

1. Adding a new note to the local state
2. Resetting the form state to clear out the form
3. Updating the form state when the user types

```javascript 

 case 'ADD_NOTE':
   return { ...state, notes: [action.note, ...state.notes]}
 case 'RESET_FORM':
   return { ...state, form: initialState.form }
 case 'SET_INPUT':
   return { ...state, form: { ...state.form, [action.name] : action.value } }
```

