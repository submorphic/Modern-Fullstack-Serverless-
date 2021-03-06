
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



## Deploying the Services

With all of the services configured, we’re ready to deploy the
backend:

```
 ~ amplify push

```

## Building the Frontend

Now that the project has been created and configured and the
backend has been deployed, we can start setting up the client!The first thing we will do is create the files we will need for this app:

```
~ cd src
~ touch Container.js Footer.js Nav.js Admin.js Router.js Performance.js Home.js
```


The next thing we will need to do is open src/index.js to add the
Amplify configuration, import the Ant Design styles, and replace the
main component with the Router that we will be creating soon.
Update the file with the following code:

```jsx
/* src/index.js */
import React from 'react';
import ReactDOM from 'react-dom';

import Router from './Router';
import 'antd/dist/antd.css';

import Amplify from 'aws-amplify';
import config from './aws-exports';

Amplify.configure(config);

ReactDOM.render(<Router />, document.getElementById('root'));

```

Container
Now, let’s create the Container component that will serve as a
reusable component to add padding and styling for our views:

```jsx
/* src/Container.js */
import React from 'react'

export default function Container({ children }) {
 return (
    <div style={container}>
    {children}
    </div>
 )
}

const container = {
  padding: '30px 40px',
  minHeight: 'calc(100vh - 120px)'
}
```

Footer

Here, we’ll create the Footer component that will serve as a
reusable component to add a basic footer, as well as a link for admins
to be able to sign up and sign in:

```jsx
/* src/Footer.js */
import React from 'react'; 
import { Link } from 'react-router-dom'; 

function Footer() {
  return (
    <div style={footerStyle}>
      <Link to="/admin">
        Admins
      </Link>
    </div>
  )
}

const footerStyle = {
  borderTop: '1px solid #ddd',
  display: 'flex',
  alignItems: 'center',
  padding: 20
}

export default Footer

```

Nav
Now, open src/Nav.js to create the basic navigation. There will only
be one link: a link back to the main view that will hold all of the
shows and performances:

```jsx
/* src/Nav.js */
import React from 'react'
import { Link } from 'react-router-dom'

import { Menu } from 'antd'
import { HomeOutlined } from '@ant-design/icons'

const Nav = (props) => {
  const { current } = props
  return (
    <div>
      <Menu selectedKeys={[current]} mode="horizontal">
        <Menu.Item key='home'>
          <Link to={`/`}>
            <HomeOutlined />Home
          </Link>
        </Menu.Item>
      </Menu>
    </div>
  )
}
export default Nav

```

Admin

The Admin component we’ll create will only do three things for now:
allow a user to sign up, sign in, and sign out. The idea for this
component is to give admins a way to sign up so they can then create
and manage the API as an admin.

If you ever need to update your backend code like the GraphQL
schema or Lambda function, you can make the changes locally, then
run amplify push to deploy the changes to the backend:

```jsx
/* src/Admin.js */

import React from 'react'
import { withAuthenticator, AmplifySignOut } from '@aws-amplify/ui-react'

import { Auth } from 'aws-amplify'
import { Button } from 'antd'


function Admin() {
  return (
    <div>
      <h1 style={titleStyle}>Admin</h1>
      <AmplifySignOut />
    </div>
  )
}

const titleStyle = {
fontWeight: 'normal',
margin: '0px 0px 10px 0px'
}

export default withAuthenticator(Admin); 
```


Router
Now let’s create the Router:

```jsx
/* src/Router.js */
import React, { useState, useEffect } from 'react'
import { HashRouter, Switch, Route } from 'react-router-dom'
import Home from './Home'
import Admin from './Admin'
import  Nav from './Nav'
import  Footer from './Footer'

import  Container from './Container'
import  Performance from './Performance'

const Router = () => {const [current, setCurrent] = useState('home')
useEffect(() => {
setRoute()
window.addEventListener('hashchange', setRoute)
return () => window.removeEventListener('hashchange',
setRoute)
}, [])
function setRoute() {
const location = window.location.href.split('/')
const pathname = location[location.length-1]
setCurrent(pathname ? pathname : 'home')
}
return (
<HashRouter>
<Nav current={current} />
<Container>
<Switch>
<Route exact path="/" component={Home}/>
<Route exact path="/performance/:id" component=
{Performance} />
<Route exact path="/admin" component={Admin}/>
</Switch>
</Container>
<Footer />
</HashRouter>
)
}

export default Router

```

In this component, we combine the router with the persistent UI
components like the Container and Footer.
The app has three routes:

Home
This is the main route that will render the stages and

performances.
PerformanceThis is this is the route that will render an individual performance
and details around the performance.

Admin
This is the route that will render the sign-up/sign-in page for
admins.

In the Performance route, you will see that we are using a path that
looks like this:
/performance/:id

Doing this allows us to have URL parameters, so if we hit a route like
this, we will be able to easily extract the ID from the URL:
/performance/100

Hitting a route with URL parameters will allow us to access them in
the component itself. This is useful because we will be using the ID
of the performance to fetch the performance details, and having them
easily accessible in the route parameters enables this. It also enables
you to easily build apps that support deep linking.
Performance

Next, let’s create the Performance component:

```jsx
/* src/Performance.js */
import React, { useState, useEffect } from 'react'
import { useParams } from 'react-router-dom'
import { getPerformance } from './graphql/queries'
import { API } from 'aws-amplify'
function Performance() {
const [performance, setPerformance] = useState(null)const [loading, setLoading] = useState(true)
let { id } = useParams()
useEffect(() => {
fetchPerformanceInfo()
}, [])
async function fetchPerformanceInfo() {
try {
const talkInfo = await API.graphql({
query: getPerformance,
variables: { id },
authMode: 'API_KEY'
})
setPerformance(talkInfo.data.getPerformance)
setLoading(false)
} catch (err) {
console.log('error fetching talk info...', err)
setLoading(false)
}
}
return (
<div>
<p>Performance</p>
{ loading && <h3>Loading...</h3>}
{
performance && (
<div>
<h1>{performance.performer}</h1>
<h3>{performance.time}</h3>
<p>{performance.description}</p>
</div>
)
}
</div>
)
}
export default Performance

```


The render method of this component is pretty basic; it’s just rendering the performance performer, time, and description. What is interesting about this component is how we get that information. We do so with the following flow:1. We create two pieces of state using the useState hook:

loading (set to true) and performance (set to null). We
also create a variable called id that uses the useParams
helper from React Router to get the route parameter of id.
2. When the component loads, we use the useEffect hook
to immediately call the fetchPerformanceInfo
function.



3. The fetchPerformanceInfo function will use the id
from the route params to call the AppSync API. The API call
here uses API.graphql, passing in the variables,
query, and the authMode. By default, our API is using
Cognito User Pools as the auth mode. Any time we would
like to override this, like in this case to make a public API
call, we need to specify the authMode in the API call itself.

4. Once the data is returned from the API, we call
setLoading and setPerformance to update the UI
and render the data coming back from the API.


Home
Now, let’s create the last component, the Home component:

```jsx

/* src/Home.js */
import React, { useEffect, useState } from 'react'
import { API } from 'aws-amplify'
import { listStages } from './graphql/queries'
import { Link } from 'react-router-dom'
import { List } from 'antd';
function Home() {
const [stages, setStages] = useState([])
const [loading, setLoading] = useState(true)
useEffect(() => {
getStages()
}, [])
async function getStages() {const apiData = await API.graphql({
query: listStages,
authMode: 'API_KEY'
})
const { data: { listStages: { items }}} = apiData
setLoading(false)
setStages(items)
}
return (
<div>
<h1 style={heading}>Stages</h1>
{ loading && <h2>Loading...</h2>}
{
stages.map(stage => (
<div key={stage.id} style={stageInfo}>
<p style={infoHeading}>{stage.name}</p>
<p style={infoTitle}>Performances</p>
<List
itemLayout="horizontal"
dataSource={stage.performances.items}
renderItem={performance => (
<List.Item>
<List.Item.Meta
title={<Link style={performerInfo}
to={`/performance/${
performance.id}`}>{
performance.performer}</Link>
}
description={performance.time}
/>
</List.Item>
)}
/>
</div>
))
}
</div>
)
}

const heading = { fontSize: 44, fontWeight: 300, marginBottom: 5 }

const stageInfo = { padding: '20px 0px 10px', borderBottom: '2px solid #ddd' }

const infoTitle = { fontWeight: 'bold' , fontSize: 18 }

const infoHeading = { fontSize: 30, marginBottom: 5 }

const performerInfo = { fontSize: 24 }

export default Home
```


The logic in this component is actually very similar to what we did in
the Performance component:
1. Create two main pieces of state using the useState hook:
stages (set to an empty array), and loading (set to
true).

2. When the app loads, we use the API class with a custom
authMode of API_KEY to call the AppSync API.

3. When the data comes back from the API, set the state for the
stages and set loading to false.
Now, the app is finished, but there’s just one more thing. Because we
have created a custom access pattern for the performances resolver,
we need to update the listStages query definition to also return
the performances. To do this, update the listStages query with
the following:


```javascript
/* src/graphql/queries.js */
export const listStages =  query ListStages(
$filter: ModelStageFilterInput
$nextToken: String
) {
listStages(filter: $filter, limit: 500, nextToken:
$nextToken) {
items {
id
name
performances {
items {
id
time
performerdescription
}
}
}
nextToken
}
}
`;
```

Now, the app is completed and we can populate some data. Start the
app and sign up with an admin user:
```
~ npm start
```

Click the Admins link in the footer to sign up. Once you’ve signed
up, open the AppSync console:

```

~ amplify console api
> Choose GraphQL

```

In the Queries panel of the console, you will need to click Login with
User Pools to sign in using the username and password of the user
you just created. When prompted for the ClientID, use the
aws_user_pools_web_client_id located in the aws-
exports.js file of your local project.

Next, create at least one stage and one performance:

```graphql

mutation createStage {
createStage(input: {
id: "stage-1"
name: "Stage 1"
}) {
id name
}
}

mutation createPerformance {
createPerformance(input: {
performanceStageId: "stage-1"
performer: "Dreek"
description: "Dreek LIVE in NYC! Don't miss out,
performing
all of the hits with a few surprise
performances!"
time: "Monday, May 4 2022"
}) {
id performer description
}
}

```
Now, our database has some data, and we should be able to view it in
our app and navigate between the main view and the detail view for
each performance!


## Conlusion

The GraphQL Transform directive enables you to add powerful features to your GraphQL API like authorization rules, relationships, and custom indexes for additional data access patterns.

The @auth directive allows you to pass in an array of rules
to define authorization rules on types and fields.

The @connection directive enables you to model
relationships between GraphQL types.

The @key directive enables you to define custom indexes for custom data access patterns and to enhance existing relationships.

When creating an API with multiple authorization types, you
will have a Primary authorization type that will be the
default when making API calls. Whenever you need to
override the Primary authorization type, you must pass in
the authMode parameter to the API class defining the
authorization type you would like to use.

