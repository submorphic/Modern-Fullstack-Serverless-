# Chapter 4,  Introduction to Authentication

Authentication and identity are integral parts of almost any
application. Knowing who the user is, what permissions they have,
whether or not they are signed in, and having a unique identifier for
the user allow your application to render the correct views and return
the proper data for the currently signed-in user.

Most applications require mechanisms to handle user sign-up, user
sign-in, password encryption, and updating, as well as countless other
tasks around identity management. Modern applications often call for
things like open authentication (OAUTH), multifactor authentication
(MFA), and time-based on time passwords (TOTP).



## Introduction to Amazon Cognito

Amazon Cognito is a fully managed identity service from AWS.
Cognito allows for simple and secure user sign-up, sign-in, access
control, and user identity management. Cognito scales to millions of
users and also supports sign-in with social identity providers, such as
Facebook, Google, and Amazon. It is also free for the first 50,000
users of any app.

### How Amazon Cognito Works
Cognito has two main pieces: user pools and identity pools:

#### User pools

These provide a secure user directory that stores all your users
and scales to hundreds of millions of users. It is a fully managed
service. As a serverless technology, user pools are easy to set up
without having to worry about standing up any infrastructure.
User pools are what manage all of the users that sign up and sign
in to the account, and are what we will be focusing on in this
chapter.

#### Identity pools

These allow you to authorize users that are signed in to your
application to access various other AWS services. Say you wanted
to allow a user to have access to a Lambda function so that they
could fetch data from another API; you could specify that while
creating an identity pool. Where user pools come in is that the
source of these identities could be a Cognito user pool or even
Facebook or Google.


Cognito user pools allow your application to invoke various methods
against the service to manage all aspects of user identity, including
such items as:

1. Signing up a user
2. Signing in a user
3. Signing out a user
4. Changing a user’s password
5. Resetting a user’s password
6. Confirming an MFA code


### Amazon Cognito Integration with AWS Amplify

AWS Amplify has support for Amazon Cognito in various ways. First
of all, you can create and configure Amazon Cognito services directly
from the Amplify CLI. Once you’ve created the authentication
service via the CLI you can then call various methods (like signUp,
signIn, and signOut) from your JavaScript application using the
Amplify JavaScript client library.

Amplify also has preconfigured UI components that allow you to
scaffold out entire authentication flows in just a couple of lines of
code for frameworks like React, React Native, Vue, and Angular.
In this chapter, you’ll be using a combination of the Amplify CLI,
Amplify JavaScript client, and Amplify React UI components to
build an application that demonstrates routing, authentication, and
protected routes. You’ll also use React Router for routing and Ant
Design to give the application some basic styling


![1](https://user-images.githubusercontent.com/23625821/120095607-1da26780-c127-11eb-9fa1-ca5ff6064f5f.png)



## Creating the React App and Adding Amplify

The first thing you’ll do to get started is create the React application,
install the necessary dependencies, and create the Amplify project.
To begin, open your terminal and create a new React application:

```javascript 
  ~ npx create-react-app basic-authentication
  ~ cd basic-authentication
```

Then install the AWS Amplify, AWS Amplify React, React Router,
and Ant Design libraries:

```javascript
 ~ npm install aws-amplify @aws-amplify/ui-react antd react-router-dom
```

Initialize a new Amplify project:

```javascript
  ~ amplify init

```

With the Amplify project now initialized, we can create the
authentication service. To do so, run the following command:

```javascript 
  ~ amplify add auth

```
Now the authentication service has been configured and you can
deploy it using the amplify push command:

```javascript
  ~ amplify push
```

### Client Authentication Overview

Using Amplify, there are two main ways to implement authentication
on the client now that the service is up and running:

##### Auth class

The Amplify client library exposes an Auth class with over 30
different methods that allow you to handle everything associated
with user management. Some examples of the methods available
are Auth.signUp, Auth.signIn, and Auth.signOut.
Using this class, you can create a completely custom
authentication flow based on your application’s requirements. To
do so, you have to manage all of the styling and application state
yourself.

##### Framework-specific authentication components

The framework-specific libraries available in Amplify for
frameworks like React, React Native, Vue, and Angular expose
higher-level abstractions for managing authentication. These
components will render an entire (customizable) authentication
flow with only a few lines of code.


## Building the App

The next step will be to go ahead and create the folder and file
structure for the app.

### Creating the File and Folder Structure

In your app, create the following files in the src directory:

```jsx
 
 Container.js
 Nav.js
 Profile.js
 
 Protected.js
 Public.js
 Router.js
```

These files do the following:

##### Container.js
This file will hold a component you will be using to apply
reusable styling to the other components.

##### Nav.js
In this component, you will create a navigation UI.

##### Profile.js
This component will render profile information about the logged-
in user. This will also be the component where we add the
authentication component for signing up and signing in.

##### Protected.js
This is the component we will be using as an example of how to
create a protected route. If the user is signed in, they will be able
to view this route. If they are not signed in, they will be redirected
to the sign-in form.

##### Public.js
This is a basic route that will be viewable whether or not the user
is signed in.

##### Router.js
This file will hold the router and some logic to determine the
current route name.


### Creating the First Component

To start, let’s create the most simple component we will be using for
the app—the Container component. This component is what we
will be using to wrap all of our other components so that we can
apply some reusable styles between the components:

```jsx
    /* src/Container.js */
 
    import React from 'react'
    const Container = ({ children }) => (
        <div style={styles.container}>
        { children }
        </div>
       )
    const styles = {
          container: {
          margin: '0 auto',
          padding: '50px 100px'
     }
 }
 
export default Container; 
```

Using this component, you can now apply consistent styling across
the entire app without having to rewrite your styles. You can then use
it like this:

```jsx
<Container>
<h1>Hello World</h1>
</Container>

```


Anything that is a child of the Container component will be
rendered with the styling set in the Container component. Doing
this allows you to have a single place that you can control the styles.
In case you want to make styling changes later, you only need to
adjust one component.

### Public Component
This component simply renders the name of the route to the UI and
can be accessed whether or not the user is signed in. In this
component, you will use the Container component to add some
padding and margin:

```jsx 
/* src/Public.js */
import React from 'react'
import Container from './Container'
function Public() {
return (
<Container>
<h1>Public route</h1>
</Container>
)
}
export default Public
```


### Nav Component

The Nav (navigation) component will be utilizing the Ant Design
library and React Router. Ant Design will provide the Menu and
Icon components to make a nice looking menu, and React Router
will provide the Link component so that we can link and navigate to
different parts of the app.


You’ll also notice that there is a current prop that is passed in to
the component. This prop represents the name of the current route.
For this application the value will either be home, profile, or
protected. The current value is used in the selectedKeys
array of the Menu component to highlight the current route in the
navigation bar. This value will be calculated in the Router
component and passed into this component as a prop:

```jsx
/* src/Nav.js */
import React from 'react'
import { Link } from 'react-router-dom'
import { Menu } from 'antd'
import { HomeOutlined, ProfileOutlined, FileProtectOutlined
} from
'@ant-design/icons'
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
<Menu.Item key='profile'>
<Link to='/profile'>
<ProfileOutlined />Profile
</Link>
</Menu.Item>
<Menu.Item key='protected'><Link to='/protected'>
<FileProtectOutlined />Protected
</Link>
</Menu.Item>
</Menu>
</div>
)
}
export default Nav
```

### Protected Component

The Protected component will be the protected, or private, route.
If the user trying to access this route is signed in, they will be able to
view this route. If they are not signed in, they will be redirected to the
profile page to sign up or sign in.

#### Auth 

This AWS Amplify class handles user identity management. You
can use this class to do everything from signing a user up andsigning them in to resetting their password. In this component we
will be calling a method,
Auth.currentAuthenticatedUser, that will check if the
user is currently signed in and, if so, return data about the signed-
in user:

```jsx
/* src/Protected.js */
import React, { useEffect } from 'react';
import { Auth } from 'aws-amplify'
import Container from './Container'
function Protected(props) {
useEffect(() => {
Auth.currentAuthenticatedUser()
.catch(() => {
props.history.push('/profile')
})
}, [])
return (
<Container>
<h1>Protected route</h1>
</Container>
);
}
export default Protected
```


When the component is rendered, we check to see if the user is signed in to the app by calling Auth.currentAuthenticatedUser in the useEffect hook. 

If this API call is not successful, that means the user is not signed in and we need to redirect them. We redirect them by calling
props.history.push('/profile').

If the user is signed in, then we take no action and allow them to view
the route.

#### Router Component
The Router component will define the components and routes we
want to have available in our application.
This component will also be setting the current route name that will
be used in the Nav component to highlight the current route based on
the window.location.href property.

The components that you will be using from React Router are
HashRouter, Switch, and Route:

##### HashRouter
This is a router that uses the hash portion of the URL (i.e.,
window.location.hash) to keep your UI in sync with the URL.

##### Switch
Switch renders the first child route that matches the location.
This is different than the default functionality of just using the
router, which may render multiple routes that match the location.

##### Route
This component allows you to define the component that you’d
like to render based on a path parameter:

```jsx 
/* src/Router.js */
import React, { useState, useEffect } from 'react'
import { HashRouter, Switch, Route } from 'react-router-dom'

import Nav from './Nav'
import Public from './Public'
import Profile from './Profile'
import Protected from './Protected'


const Router = () => {
const [current, setCurrent] = useState('home')
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
<Switch>
<Route exact path="/" component={Public}/>
<Route exact path="/protected" component={Protected}
/>
<Route exact path="/profile" component={Profile}/>
<Route component={Public}/>
</Switch>
</HashRouter>
)
}
```

#### Profile Component

The last component we need to finish our app is the Profile component. This component will do several things:

1. Render the authentication form if the user is not signed in.
2. Provide a sign-out button.
3.Render the user’s profile information to the UI.


To sign the user out, we use the AmplifySignOut UI component.
This component will sign the user out and re-render the UI to show
the authentication form.

To display the user profile data, we use the
Auth.currentAuthenticatedUser method. If the user is
signed in, this method will return the user profile data along with
information about the session.
The information that we are interested in using for the profile are the username and user attributes, which include the phone number, email, and any other information gathered when the user signed up:

```javascript 
/* src/Profile.js */
import React, { useState, useEffect } from 'react'
import { Auth } from 'aws-amplify'

import { withAuthenticator, AmplifySignOut } from '@aws-amplify/ui-react'
import Container from './Container'

function Profile() {
useEffect(() => {
checkUser()
}, [])

const [user, setUser] = useState({})
async function checkUser() {
try {
const data = await Auth.currentUserPoolUser()
const userInfo = { username: data.username,
...data.attributes, }
setUser(userInfo)
} catch (err) { console.log('error: ', err) }
}
return (
<Container>
<h1>Profile</h1>
<h2>Username: {user.username}</h2>
<h3>Email: {user.email}</h3>
<h4>Phone: {user.phone_number}</h4>
<AmplifySignOut />
</Container>
);
}

export default withAuthenticator(Profile); 

```

#### Styling the UI Components
Under the hood, the Amplify UI components are implemented using
Web Components. This means we can target them as a first-class
HTML element for CSS styling. We want our UI components to
match the blue colors in the rest of our app. To do so, we can add the
following CSS property to the bottom of index.css to define the
colors we’d like to use:

```css
/* src/index.css */
:root {
--amplify-primary-color: #1890ff;
--amplify-primary-tint: #1890ff;
--amplify-primary-shade: #1890ff;
}
```

#### Configuring the AppNow the app is built.
The last thing we need to do is update index.js to import the Router and add the Amplify configuration.
We also want to import the necessary CSS for the Ant Design library:

```javascript 

/* src/index.js */

import React from 'react';
import ReactDOM from 'react-dom';

import './index.css';
import Router from './Router';

import 'antd/dist/antd.css';
import Amplify from 'aws-amplify'
import config from './aws-exports'

Amplify.configure(config); 

ReactDOM.render(<Router />, document.getElementById('root'));

```


To test the app, we can now run the start command:
```javascript 
  ~ npm start
```

### Conclusion

Congratulations, you’ve built out an authentication flow with routing and protected routes! 

Here are a few things to keep in mind from this part:

1. Use the withAuthenticator HOC to quickly get up and running with a preconfigured authentication flow.

2.  Use the Auth class for more fine-grained control over authentication and to get data about the currently signed-in user.

3.  Ant Design helps you get started with preconfigured design without having to write any style-specific code.
 
