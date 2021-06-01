# Chapter 6,  Serverless Functions In-Depth


In previous parts, you learned how to create and interact with a serverless
API using API Gateway and AWS Lambda. Here, you’ll continue to
learn about how to use serverless functions by creating two new types
of functions. The functions in this part will be different in that,
instead of using them as strictly a web server or an API, you’ll be
using them to interact with other AWS services to aid in the
application-development process.


You’ll be creating the following two kinds of functions in this
part:

#### A function that dynamically adds a user to a group based on their email address

In some applications, you will need to perform “coarse-grained”
access control, which typically means granting certain
permissions to users in a broad way based on the type of role or
group they are associated with. In our example, we’ll have an
administrator group of users that will be identified by their email
address. If a user signs up with one of these email addresses, we
will place them in a group called Admin.

#### A function that automatically resizes an image after it has been uploaded to Amazon S3

Many applications require dynamic image resizing on the server
after a user has uploaded an image. This is done for many
reasons, ranging from the need to make the web application more
performant by compressing images to the need to dynamically
create avatars or thumbnail images of a smaller size for images.

## Event Sources and Data Structure

In part 2, we briefly talked about event sources for serverless functions as part of an event-based architecture.

The only event source we have implemented up until this point has been from API Gateway: an HTTP request that triggered the function, and fetched
data from an API and returned it in the response.

In this part, we’ll be working with two other event types and sources, one from Amazon S3 and one from Amazon Cognito. 

To understand the events coming into Lambda from the event
sources, it’s important to underscore the following point: the shape of
the event data will differ between different event types. For instance,
the HTTP event data structure coming from API Gateway will be
different than the Amazon S3 event data structure, and the Amazon
S3 event data structure will differ from the Amazon Cognito data
structure.


### API Gateway Event

The API gateway event data is the data structure that will be passed
into the Lambda function when invoking it from an API Gateway
HTTP event, like GET, PUT, POST, or DELETE. This data structure
holds information like the HTTP method that invoked the function,
the path that was invoked, the body if one was passed in, and the
identity of the user calling the API (inside the
requestContext.identity field) if the user was
authenticated:

```javascript 
{
    "resource": "/items",
    "path": "/items",
    "httpMethod": "GET",
    "headers": { /* header info */ },
    "multiValueHeaders": { /* multi value header info */ },
    "queryStringParameters": null,
    "multiValueQueryStringParameters": null,
    "pathParameters": null,
    "stageVariables": null,
    "requestContext": {
    "resourceId": "b16tgj",
    "resourcePath": "/items",
    "httpMethod": "GET",
    "extendedRequestId": "CzuJMEDMoAMF_MQ=","requestTime": "07/Nov/2019:21:46:09 +0000",
    "path": "/dev/items",
    "accountId": "557458351015",
    "protocol": "HTTP/1.1",
    "stage": "dev",
    "domainPrefix": "eq4ttnl94k",
    "requestTimeEpoch": 1573163169162,
    "requestId": "1ac70afe-d366-4a52-9329-5fcbcc3809d8",
    "identity": {
    "cognitoIdentityPoolId": "",
    "accountId": "",
    "cognitoIdentityId": "",
    "caller": "",
    "apiKey": "",
    "sourceIp": "192.168.100.1",
    "cognitoAuthenticationType": "",
    "cognitoAuthenticationProvider": "",
    "userArn": "",
    "userAgent": "Mozilla/5.0 (Macintosh; Intel Mac OS
    X 10_11_6)
    AppleWebKit/537.36 (KHTML, like Gecko)
    Chrome/52.0.2743.82
    Safari/537.36 OPR/39.0.2256.48",
    "user": ""
    },
    "domainName": "eq4ttnl94k.execute-api.us-east-
    1.amazonaws.com",
    "apiId": "eq4ttnl94k"
    },
    "body": null,
    "isBase64Encoded": false
}


```

### Amazon S3 Event

The Amazon S3 event is the data structure that will be received when
invoking a Lambda function from a file upload or update to Amazon
S3. This data structure holds an array of records from S3. The main
information you’ll typically be working with in this event data is the
s3 field. This property holds information like the bucket name, the
key, and the size of the item being stored:

```javascript 
{
        "Records": [
        {
            "eventVersion": "2.1",
            "eventSource": "aws:s3",
            "awsRegion": "us-east-2",
            "eventTime": "2019-09-03T19:37:27.192Z",
            "eventName": "ObjectCreated:Put",
            "userIdentity": {
            "principalId": "AWS:AIDAINPONIXQXHT3IKHL2"
        },
        "requestParameters": {
        "sourceIPAddress": "205.255.255.255"
        },
        "responseElements": {
        "x-amz-request-id": "D82B88E5F771F645",
        "x-amz-id-2":
        "vlR7PnpV2Ce81l0PRw6jlUpck7Jo5ZsQjryTjKlc5aLWGVHPZLj
        5NeC6qMa0emYBDXOo6QBU0Wo="
        },
        "s3": {
        "s3SchemaVersion": "1.0",
        "configurationId": "828aa6fc-f7b5-4305-8584-
        487c791949c1",
        "bucket": {
        "name": "lambda-artifacts-deafc19498e3f2df",
        "ownerIdentity": {
        "principalId": "A3I5XTEXAMAI3E"
        },
        "arn": "arn:aws:s3:::lambda-artifacts-
        deafc19498e3f2df"
        },
        "object": {
        "key": "b21b84d653bb07b05b1e6b33684dc11b",
        "size": 1305107,
        "eTag": "b21b84d653bb07b05b1e6b33684dc11b",
        "sequencer": "0C0F6F405D6ED209E1"
       }
      }
     }
   ]
}

```

### Amazon Cognito Event

The Amazon Cognito event data is the data structure that will be passed into the function when being invoked from an Amazon Cognito action. 

These actions could be anything from a user signing up, a user confirming their account, or a user signing in, among other available events:


```javascript 
{
"version": "1",
"region": "us-east-1",
"userPoolId": "us-east-1_uVWAMpQuY",
"userName": "dabit3",
"callerContext": {
"awsSdkVersion": "aws-sdk-unknown-unknown",
"clientId": "2ects9inqraapp43ejve80pv12"
},
"triggerSource": "PostConfirmation_ConfirmSignUp",
"request": {
"userAttributes": {
"sub": "164961f8-13f7-40ed-a8ca-d441d8ec4724",
"cognito:user_status": "CONFIRMED",
"email_verified": "true",
"phone_number_verified": "false",
"phone_number": "+16018127241",
"email": "dabit3@gmail.com"
}
},
"response": {}
}
```

You’ll be using these events and the information contained within
them to perform different types of actions from within the functions.


## IAM Permissions and Trigger Configuration

1. When setting up these triggers using the CLI, a couple of things are
happening under the hood:The CLI is enabling the trigger itself in the Lambda
configuration. When a trigger is enabled, the event will be
sent to the function every time that interaction happens (API
event, S3 upload, etc.).

2. The CLI is giving additional permissions to the function
itself to interact with other services. For instance, when we
enable the S3 trigger in this chapter, we are wanting the
Lambda function to be able to read and store images in that bucket.

3. To enable this, the CLI will add additional Identity and
Access Management (IAM) policies under the hood to the
function, giving it permissions like read and write access to
work with S3, or permissions to interact with the Cognito
user pool in our other example.


### Creating the Base Project

The first thing we’ll do is create a new React application and install
the dependencies we’ll need for this chapter:
```
 ~ npx create-react-app lambda-trigger-example
 ~ cd lambda-trigger-example
 ~ npm install aws-amplify @aws-amplify/ui-react uuid
```
Next, we’ll create a new Amplify project:

```
 ~ amplify init
 # walk through the steps like we've done in the previous projects
```
Now that the project has been initialized, we can begin adding the
services. The services we’ll need for this chapter will be Amazon


Cognito, Amazon S3, and AWS Lambda. We’ll start by adding
Amazon Cognito and testing out a post-confirmation Lambda trigger.


### Adding a Post-Confirmation Lambda Trigger

The next thing we want to do is create an authentication service. We
will then create and configure a post-confirmation Lambda trigger.

This means we want a Lambda function to be invoked every time
someone successfully signs up and confirms their account using our
authentication service. This post-confirmation trigger only fires once
per confirmed user:

```javascript
~ amplify add auth
? Do you want to use the default authentication and security
configuration?
Default configuration
? How do you want users to be able to sign in? Username
? Do you want to configure advanced settings? Yes
? What attributes are required for signing up? Email
? Do you want to enable any of the following capabilities?
Add User to Group
? Enter the name of the group to which users will be added.
Admin
? Do you want to edit your add-to-group function now? Y

```

Now, update the function with the following code:


```javascript 

// amplify/backend/function/<function_name>/src/add-to-group.js


        const aws = require('aws-sdk');
        
        exports.handler = async (event, context, callback) => {
            const cognitoProvider = new aws.CognitoIdentityServiceProvider({apiVersion: '2016-04-18'});
            let isAdmin = false
            const adminEmails = ['dabit3@gmail.com']
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
           groupParams.GroupName = 'Admin',
           userParams.GroupName = 'Admin'
           
            // First check to see if the group exists, and if not create the group
        
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
          callback(null, event)}
       }
```

In this function, there is one main piece of functionality.

If the user is one of the admins specified in the admins email array, we automatically place them in the group called Admins.

Change the values in the adminEmails array to include your email address.

To deploy the service, run the push command:

```
  ~ amplify push
```

Now that the backend is set up, we can test it out. To do so, we first
need to configure the React project to recognize the Amplify
dependencies. Open src/index.js and add the following below the last
import:

```javascript
    
    import Amplify from 'aws-amplify';
    import config from './aws-exports';
    
    Amplify.configure(config);
```

Next, we’ll sign up a new user and display a greeting if they are an
admin. To do so, open src/App.js and add the following:

```jsx
    
    import React, { useEffect, useState } from 'react';
    import { Auth } from 'aws-amplify';
    
    import { withAuthenticator, AmplifySignOut } from '@aws-amplify/ui-react';
    import './App.css'
    
    function App() {
        const [user, updateUser] = useState(null)
        useEffect(() => {
            Auth.currentAuthenticatedUser()
            .then(user => updateUser(user))
            .catch(err => console.log(err));
        }, []); 

        let isAdmin = false; 
        if (user) {
           const { signInUserSession: { idToken: { payload }} } = user; 

           console.log('payload: ', payload)
        if ( payload['cognito:groups'] && payload['cognito:groups'].includes('Admin') ) {
            isAdmin = true
        }
      }
       return (
            <div className="App">
                <header>
                    <h1>Hello World</h1>
                    { isAdmin && <p>Welcome, Admin</p> }
                </header>
                <AmplifySignOut />
            </div>
          );
    }

    export default withAuthenticator(App);
    
```

## Dynamic Image Resizing with AWS Lambda and Amazon S3

In the next example, we will add functionality that allows users to
upload images to Amazon S3. We’ll also configure an S3 trigger to
call a Lambda function every time a file is uploaded to the bucket.

In this function, we’ll check the size of the image, and if it is above a
certain width, we will resize it to be below the width threshold.

For this to work, we need to enable S3 to trigger the Lambda function
in our project when a file is uploaded. We can do this using the
Amplify CLI by just creating the S3 bucket and choosing the correct
configuration. From the CLI, run the following commands:

```
  ~ amplify add storage
```

### Adding the Custom Logic for Resizing the Image

Now, we can update the function to implement the image resizing.
In this function, we will fetch the image from S3 when the event
comes through, and check to see if it is greater than 1,000 pixels
wide. If that’s the case, then we’ll resize it to 1,000 pixels wide and
save it back to the S3 bucket. If the image is not larger than 1,000
pixels wide, we exit from the function without taking any action:

```javascript
// amplify/backend/function/<functionname>/src/index.js

// Import the sharp library

const sharp = require('sharp')
const aws = require('aws-sdk')
const s3 = new aws.S3()

exports.handler = async function (event, context) {
 //eslint-disable-line
 // If the event type is delete, return from the function
 if (event.Records[0].eventName === 'ObjectRemoved:Delete') return; 
 
 // Next, we get the bucket name and the key from the event.

 const BUCKET = event.Records[0].s3.bucket.name
 const KEY = event.Records[0].s3.object.key
 try {
  // Fetch the image data from S3
   let image = await s3.getObject({ Bucket: BUCKET, Key: KEY }).promise(); 
   
   image = await sharp(image.Body)
   // Get the metadata from the image, including the width and the height
   
   const metadata = await image.metadata()
   if (metadata.width > 1000) {
   // If the width is greater than 1000, the image is resized
   
     const resizedImage = await image.resize({ width: 1000 }).toBuffer(); 
     
     await s3.putObject({Bucket: BUCKET, Body: resizedImage, Key: KEY }).promise() 
     return;
    } else {
        return
     }
    }
     catch(err) {
       context.fail(`Error getting files: ${err}`);
    }
};

```

For our function to work, we need to do one more thing. We are
requiring the Sharp library in our Lambda function, but so far we
have not installed this dependency. To make sure this module is
installed, update the package.json file for the function to add both the
dependency for the package as well as an install script that we will
need in order for Sharp to run correctly in the Lambda environment.
The two fields we will be adding are scripts and
dependencies:

```json 
// amplify/backend/function/<functionname>/src/package.json
{
    "name": "your-function-name",
    "version": "2.0.0",
    "description": "Lambda function generated by Amplify",
    "main": "index.js",
    "license": "Apache-2.0",
    "scripts": {
    "install": "npm install --arch=x64 --platform=linux --
    target=10.15.0 sharp"
},
    "dependencies": {
    "sharp": "^0.23.2"
    }
}
```


Now, the service is ready to be deployed:
```
~ amplify push
```

### Uploading Images from the React Application

Next, open src/App.js and add the following code to render an image
picker and photo list:

```javascript
import React, { useState, useEffect} from 'react'; 
import { Storage } from 'aws-amplify'; 

import { v4 as uuid } from 'uuid'; 
import './App.css'; 

function App() {
    const [images, setImages] = useState([])
    useEffect(() => {
    fetchImages()
    }, [])
    async function onChange(e) {
    /* When a file is uploaded, create a unique name and
    save it using
    the Storage API */
    const file = e.target.files[0];
    const filetype = file.name.split('.')
    [file.name.split.length - 1]
    await Storage.put(`${uuid()}.${filetype}`, file)
    /* Once the file is uploaded, fetch the list of images
    */
    fetchImages()
    }
    async function fetchImages() {
    /* This function fetches the list of image keys from
    your S3 bucket */
    const files = await Storage.list('')
    /* Once we have the image keys, the images must be
    signed in order
    for them to be displayed */
    const signedFiles = await Promise.all(files.map(async
    file => {
    /* To sign the images, we map over the image key array
    and get a
    signed url for each image */
    const signedFile = await Storage.get(file.key)
    return signedFile
    }))
    setImages(signedFiles)}
return (
    <div className="App">
        <header className="App-header">
            <input
               type="file"
               onChange={onChange}
            />
        {
          images.map(image => (
            <img
                src={image}
                key={image}
                style={{ width: 500 }}
            />
          ))
        }
        </header>
    </div>
  );
}
export default App; 

```


Next, run the app:
```
 ~ npm start

```

When you upload an image that is wider than 1,000 pixels, you’ll
notice that it will initially load as the original size, but if you reload
the app, you will see that the image has been resized to the correct
1,000-pixel width.

## Conclusion

Congratulations, you’ve now successfully implemented two types of
Lambda triggers!Here are a few things to keep in mind from this chapter:

1. Lambda functions can be invoked from many different event
types, including API calls, image uploads, database
operations, and authentication events.

2. The event data structure differs based on the type of event
invoking the Lambda function.
Understanding the data available in the event variable
enables you to better evaluate the things that can be
accomplished within the function.

3. When a Lambda trigger is enabled by the Amplify CLI,
additional IAM permissions are given to the function,
allowing it to directly interact with other services.
