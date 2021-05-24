## Chapter 1,  Fullstack development in the era of cloud computing

### Modern Serverless Philosophy: 


The term serverless is commonly associated with FaaS. Though you
will find varying definitions as to what it means, the term has recently
grown to encompass more of a philosophy than a shared definition.

Many times when people talk about serverless, they are really
describing how to most efficiently deliver business value with a focus
on writing business logic, instead of coding supporting infrastructure
for your business logic. 

Adopting a serverless mindset allows you to
do this by consciously going out of your way to find and leverage
FaaS, managed services, and smart abstractions, while only building
custom solutions if an existing service just doesn’t yet exist.


More and more companies and developers are taking this approach,
as it doesn’t make sense to reinvent the wheel. With the increase in
popularity of this philosophy, there has also been an explosion of
services and tools made available from startups and cloud providers
to provide offerings that simplify backend complexity.


For an academic take on what serverless means, you may wish to
read the 2019 paper written by a group at UC Berkeley, “Cloud
Programming Simplified: A Berkeley View on Serverless
Computing,” . 

In this paper, the authors expanded the definition of
serverless:

While cloud functions—packaged as FaaS (Function as a Service)
offerings—represent the core of serverless computing, cloud
platforms also provide specialized serverless frameworks that
cater to specific application requirements as BaaS (Backend as a
Service) offerings. Put simply, serverless computing = FaaS +
BaaS.

Backend as a service (BaaS) typically refers to managed services like
databases (Firestore, Amazon DynamoDB), authentication services
(Auth0, Amazon Cognito), and artificial intelligence services
(Amazon Rekognition, Amazon Comprehend), among other managed
services. 

Berkeley’s redefinition of what serverless means
underscores what is happening in the broader spectrum of this
discussion as cloud providers begin to build more and better-managed
services and put them in this bucket of serverless.


### Characteristics of a Serverless Application

Now that you understand something about the philosophy around
serverless, what are some of the characteristics of a serverless
application?

Serverless is following some traits and characteristics that are
generally agreed upon by the industry.

#### DECREASED OPERATIONAL RESPONSIBILITIES

Serverless architectures typically allow you to shift more of your
operational responsibilities to a cloud provider or third party.

When you decide to implement FaaS, the only thing you should have
to worry about is the code running in your function. 

All of the server patching, updating, maintaining, and upgrading is no longer your responsibility. 

This goes back to the core of what cloud computing, and by extension serverless, attempts to offer: a way to spend less time managing infrastructure and spend more time building features and delivering business value.


#### HEAVY USE OF MANAGED SERVICES

Managed services usually assume responsibility for providing a
defined set of features. 

They are serverless in the sense that they scale
seamlessly, don’t require any server operations or need to manage
uptime, and, most importantly, are essentially codeless.

#### Benefits of a Serverless Architecture 

##### SCALABILITY

One of the primary advantages of going serverless is out-of-the-box
scalability. When building your application, you don’t have to worry
about what would happen if the application becomes wildly popular
and you onboard a large number of new users quickly—the cloud
provider will handle this for you.

The cloud provider automatically scales your application, running the
code in response to each interaction. In a serverless function, your
code runs in parallel and individually processes each trigger (in turn,
scaling with the size of the workload).

Not having to worry about scaling your servers and databases is a
great advantage. It’s one less thing you have to worry about when
architecting your application. 

##### COST

The pricing models of serverless architectures and traditional cloud-
based or on-premises infrastructures differ greatly.

With the traditional approach, you often paid for computing resources
whether or not they were utilized. 

This meant that if you wanted to make sure your application would scale, you needed to prepare for the largest workload you thought you might see regardless of whether you actually reached that point paying for unused resources for the majority of the life of your application.

With serverless technologies, you pay only for what you use. With
FaaS, you’re billed based on the number of requests for your
functions, the time it takes for your function code to execute, and the
reserved memory for each function. With managed services like
Amazon Rekognition, you are only charged for the images processed
and minutes of video processed, etc.—again paying only for what
you use.

This allows you to build features and applications with essentially no
up-front infrastructure costs. Only if your application begins seeing
increasing adoption and scaling do you begin to have to pay for the
service.

The bill from your cloud provider is only one part of the total cost of
your cloud infrastructure—there’s also the operations’ salaries. That
cost decreases if you have fewer ops resources.

In addition, building applications in this way usually facilitates a
faster time to market, decreasing overall development time and,
therefore, development costs.


##### DEVELOPER VELOCITY

With fewer features to build, developer velocity increases. 
Being able to spin up the types of features that are typical for most applications (e.g., databases, authentication, storage, and APIs) allows you toquickly focus on writing the core functionality and business logic for the features that you want to deliver.


##### EXPERIMENTATION

If you are not investing a lot of time building out repetitive features,
you are able to experiment more easily and with less risk.

When shipping a new feature, you often assess the risk (time and
money involved with building the feature) against the possible return
on investment (ROI). 

As the risk involved in trying out new things decreases, you are free to test out ideas that in the past may not have seen the light of day.

A/B testing (also known as bucket testing or split testing) is a way to
compare multiple versions of an application to determine which one
performs best. 

Because of the increase in developer velocity, serverless applications usually enable you to A/B test different ideas much more quickly and easily.


##### LESS CODE

Most engineers will agree that, at the end of the day, code is a
liability. What has value is the feature that the code delivers, not the
code itself. When you find ways to deliver these features while
simultaneously limiting the amount of code you need to maintain, and
even doing away with the code completely, you are reducing overall
complexity in your application.

With less complexity comes fewer bugs, easier onboarding for new
engineers, and overall less cognitive load for those maintaining and
adding new features. A developer can hook into these services and
implement features with no knowledge of the actual backend
implementation and with little to no backend code at all.


## Different Implementations of Serverless

Let’s take a look at the different ways that you can build serverless
applications as well as some of the differences between them.

### SERVERLESS FRAMEWORK

One of the first serverless implementations, the Serverless
Framework, is the most popular. It is a free and open source
framework, launched in October 2015 under the name JAWS, and
written using Node.js. At first, the Serverless Framework only
supported AWS, but then it added support for cloud providers like
Google and Microsoft Azure, among others.
The Serverless Framework utilizes a combination of a configuration
file (serverless.yml), CLI, and function code to provide a nice
experience for people wanting to deploy serverless functions and
other AWS services to the cloud from a local environment. Getting up
and running with the Serverless Framework can present a somewhat
steep learning curve, especially for developers new to cloud
computing. There is much terminology to learn and a lot that goes
into understanding how cloud services work in order to build
anything more than just a “Hello World” application.
Overall, the Serverless Framework is a good option if you understand
to some extent how cloud infrastructure works, and are looking for
something that will work with other cloud providers in addition to
AWS.

### THE AWS SERVERLESS APPLICATION MODEL

The AWS Serverless Application Model (AWS SAM) is an open
source framework, released November 18, 2016, and built and
maintained by AWS and the community. This framework only
supports AWS.

SAM allows you to build serverless applications by defining the API
Gateway APIs, AWS Lambda functions, and Amazon DynamoDB
tables needed by your serverless application in YAML files.
It uses a combination of YAML configuration and function code and a CLI to
create, manage, and deploy serverless applications.

One advantage of SAM is that it is an extension of AWS
CloudFormation, which is very powerful and allows you to do almost
anything in AWS. This can also be a disadvantage to developers new
to cloud computing and not familiar with AWS services, permissions,
roles, and terminology, as you have to already be familiar with how
the services work, the naming conventions to set them up, and how to
wire it all together.

SAM is a good choice if you are familiar with AWS and are only
deploying your serverless applications to AWS.


### AMPLIFY FRAMEWORK

The Amplify Framework is a combination of four things: CLI, client
library, toolchain, and web-hosting platform. Amplify’s purpose is to
provide an easy way for developers to build and deploy full stack
web and mobile applications that leverage the cloud. It enables not
only features such as serverless functions and authentication, but alsoGraphQL APIs, machine learning (ML), storage, analytics, push
notifications, and more.

Amplify provides an easy entry point into the cloud by doing away
with terminology and acronyms that may be unfamiliar to newcomers
to AWS and instead uses a category-name approach for referring to
services. Rather than referring to the authentication service as
Amazon Cognito, it’s referred to as auth, and the framework just uses
Amazon Cognito under the hood.

HOW DO THE FOUR PIECES FIT TOGETHER?

The CLI allows you to create, configure, and deploy cloud services from the command line.

The Client library allows you to connect to and interact with these cloud services from your web or mobile application.

The toolchain helps facilitate and speed development by doing things like generating code and serverless function boilerplates.

The hosting platform allows you to deploy your application to a live domain complete with atomic deployments, continuous integration (CI), continuous deployment (CD), custom domains, and more.


### Full Stack Serverless on AWS

Full stack serverless is about providing developers with everything
needed on both ends of the stack to accomplish their objective of
building scalable applications as quickly as possible. Here, we’ll look
at how you can build applications in this way using AWS tools and
services.

#### Amplify CLI

If you’re starting out with AWS, the sheer number of services can be
overwhelming. In addition to the many services to sort between, each
service often has its own steep learning curve. To help ease this, AWS
has created the Amplify CLI.
The Amplify CLI provides an easy entry point for developers
wanting to build applications on AWS. The CLI allows developers to
create, configure, update, and delete cloud services directly from their
frontend environment.

Instead of a service-name approach (as used by the AWS Console and
many other tools, like CloudFormation), the CLI takes a category-
name approach. AWS has many service names (for example, Amazon
S3, Amazon Cognito, and Amazon Pinpoint), which can be confusing
to new developers. Rather than using the service names to create andconfigure these services, the CLI uses names like storage (Amazon
S3), auth (Amazon Cognito), and analytics (Amazon Pinpoint) to
give you a way to understand what the service actually does versus
simply giving the service name.

The CLI has a host of commands that allow you to create, update,
configure, and remove services without having to leave your frontend
environment. You can also spin up and deploy new environments
using the CLI in order to test out new features without affecting the
main environment.

Once you’ve created and deployed features using the CLI, you can
then use the Amplify client libraries to begin interacting with the
services from your client-side application.


#### AMPLIFY CLIENT

Building full stack applications requires a combination of both client-
side tooling and backend services. In the past, the main way to
interact with AWS services was using an AWS software development
kit (SDK) such as Java, .NET, Node.js, and Python. These SDKs
work well, but none of them are particularly well-suited for client-
side development. Before Amplify, there was no simple method for
building client-side applications using AWS. If you look at the
documentation for the AWS Node.js SDK, you’ll also notice that it
presents a steep learning curve for developers new to AWS.
The Amplify client is a library made especially to provide an easy-to-
use API for JavaScript applications that need to interact with AWSservices. Amplify also has client SDKs for React Native, native iOS,
and native Android.

The approach that the Amplify client takes is to provide a higher
level of abstraction and bake in best practices to provide a
declarative, easy-to-use API. At the same time, it gives you full
control over the interactions with your backend. It’s also built
especially with the client in mind, with features like WebSocket and
GraphQL subscription support. It utilizes localStorage for the
browser and AsyncStorage for React Native to store security tokens
like IdTokens and AccessTokens to persist user authentication.
Amplify also provides UI components for popular frontend and
mobile frameworks including React, React Native, Vue, Angular,
Ionic, native Android, and native iOS. These framework-specific
components allow you to quickly get up and running with common
features like authentication and complex object storage and retrieval
without having to build out the frontend UI and deal with state.
The Amplify Framework does not support the entire suite of AWS
services; instead, it supports a subset of them with almost all of them
falling into the category of serverless. Using Amplify, it wouldn’t
make much sense to offer support for interacting with with EC2, but
it makes a lot of sense to offer support for working with
Representational State Transfer (REST) and GraphQL APIs.
Amplify was created as an end-to-end solution to fill a previously
unfilled gap, but it also encompasses a new way to build full stack
cloud applications. 

#### AWS APPSYNC

AWS AppSync is a managed API layer that uses GraphQL to make it
easy for applications to interact with any data source, REST API, or
microservice.

The API layer is one of the most important parts of an application.
Modern applications typically interact with a large number of
backend services and APIs; things like databases, managed services,
third-party APIs, and storage solutions, among others. Microservice
architecture is the usual term used for a large application built using a
combination of modular components or services.

Most services and APIs will have varying implementation details,
which creates a challenge when you’re working with a microservice
architecture. This leads to inconsistent and sometimes messy code, as
well as more cognitive load on the frontend developers making
requests to these APIs.

One good approach to working with a microservice architecture is to
provide a consistent API gateway layer that then takes all of the
requests and forwards them on to the backend services.
This allows a consistent interaction layer for your client to interact with, making development easier on the frontend.

GraphQL, a technology created and open sourced by Facebook,
offers an especially good abstraction for creating an API gateway.

GraphQL introduces a defined and consistent specification for
interacting with APIs in the form of three operations: queries (reads),
mutations (writes/updates), and subscriptions (real-time data). 

Theseoperations are defined as part of a main schema that also provides a contract between the client and the server in the form of GraphQL
types. GraphQL operations are not bound to any specific data source, so you as a developer are free to use them to interact with anything 
from a database, an HTTP endpoint, a microservice, or even a serverless function.

Typically, when building a GraphQL API, you need to deal with
building, deploying, maintaining, and configuring your own API.
With AWS AppSync, you can instead offload the server and API
management as well as the security to AWS.

Modern applications often also have concerns such as real-time and
offline support. Another benefit of AppSync is that it has built-in
support for offline (Amplify client SDKs) as well as real time
(GraphQL subscriptions) to enable developers to build these types of
applications.

In this book, you will be using AWS AppSync along with various
data sources (like DynamoDB for NoSQL and AWS Lambda for
serverless functions) as the main API layer.


### Installing and Configuring the Amplify CLI

To get started, you first need to install and configure the Amplify
CLI:
~ npm install -g @aws-amplify/cli

NOTE:

To use the CLI, you will first need to have Node.js version 10.x or greater and
npm version 5.x or greater installed on your machine.

To install Node.js, it's recommended either visiting the Node.js installation page and following the installation instructions or using Node Version Manager (NVM).

After the CLI has been installed, you next need to configure it with
an identity and access management (IAM) user in your AWS account.
To do so, you’ll configure the CLI with a reference to a set of user
credentials (access key ID and secret access key). Using these
credentials, you’ll be able to create AWS services on behalf of this
user directly from the CLI.
To create a new user and configure the CLI, you’ll run the
configure command:

~ amplify configure

This will walk you through the following steps:

1. Specify the AWS region.
This will allow you to choose the region in which you’d like
to create your user (and, by extension, the services
associated with this user). Choose the region closest to you
or a preferred region.

2. Specify the username.
This name will be the local reference of the user that you
will be creating in your AWS account. I suggest using a
name that you’ll be able to recognize later when referencing
it, such as amplify-cli-us-east-1-user or mycompany-cli-
admin.

Once you enter your name, the CLI will open up the AWS IAM
dashboard. From here, you can accept the defaults by clicking Next:
Permissions, Next: Tags, Next: Review, and Create user to create the
IAM user.


### Create a project 

terminal: 
```javascript
~ npx create-react-app react-amplify-project

~ cd react-amplify-project 

```

Next, you can create an Amplify project. To do so, you’ll run the
init command:

```javascript
~ amplify init
```

This will walk you through the following steps:

1. Enter a name for the project.
This will be the local name for the project, usually
something that describes what the project is or what it does.

2. Enter a name for the environment.
This will be a reference to the initial environment that you
will be working in. Typical environments in this workflow
could be something like dev, local, or prod but could be
anything that makes sense to you.

3. Choose your default editor.
This will set your editor preference. The CLI will later use
this preference to open your text editor with files that are
part of the current project.

4. Choose the type of app that you’re building.This will determine whether the CLI should configure, build,
and run commands if you are using JavaScript. For this
example, choose javascript.

5. What JavaScript framework are you using?
This will determine a few base build and start commands.
For this example, choose react.

6. Choose your source directory path.
This allows you to set the directory where your source code
will live. For this example, choose src.

7. Choose your distribution directory path.
For web projects, this will be the folder containing the
complied JavaScript source code as well as your favicon,
HTML, and CSS files. For this example, choose build.

8. Choose your build command.
This specifies the command for compiling and bundling your
JavaScript code. For this example, use npm run-script build.

9. Choose your start command.
This specifies the command to server your application
locally. For this example, use npm run-script start.

10. Do you want to use an AWS profile?
Here, choose Y and then pick the AWS profile you created
when you ran amplify configure.


Now, the Amplify CLI will initialize your new Amplify project.
When the initialization is complete, you will have two additional
resources created for you in your project: a file called aws-exports
located in the src directory and a folder named amplify located in
your root directory.The aws-exports file
The aws-exports file is a key-value pairing of the resource
categories created for you by the CLI along with their credentials.

The amplify folder

This folder holds all of the code and configuration files for your
Amplify project. In this folder you’ll see two subfolders: the
backend and #current-cloud-backend folders.

The backend folder

This folder contains all of the local code for your project such
as the GraphQL schema for an AppSync API, the source code
for any serverless functions, and infrastructure as code
representing the current local status of the Amplify project.

The #current-cloud-backend folders

This folder holds the code and configurations that reflect what
resources were deployed in the cloud with your last Amplify
push command. It helps the CLI differentiate between the
configuration of the resources already provisioned in the
cloud and what is currently in your local backend directory
(which reflects your local changes).


### Creating and Deploying Your First Service

To create a new service, you can use the add command from
Amplify:

```javascript 
~ amplify add auth 
```

This will walk you through the following steps:

1. Do you want to use the default authentication and security
configuration?

This gives you the option of creating an authentication
service using a default configuration (MFA on sign-up,
password at sign-in), creating an authentication
configuration with social providers, or creating a completely
custom authentication configuration. For this example,
choose Default configuration.

2. How do you want users to be able to sign in?
This will allow you to specify the required sign-in property.
For this example, accept the default by choosing Username.

3. Do you want to configure advanced settings?
This will allow you to walk through additional advanced
settings for things like additional sign-up attributes and
Lambda triggers. You do not need any of these for this
example, so accept the default by choosing No, I am done.
Now, you’ve successfully configured the authentication
service and are now ready to deploy. To deploy the
authentication service, you can run the push command:

```javascript 
~ amplify push 
``` 
4. Are you sure you want to continue?
Choose Y.


After the deployment is complete, your authentication service has
successfully been created. Congratulations, you’ve deployed your
first feature. Now, let’s test it out.

There are several ways to interact with the authentication service in a
React application. You can use the Auth class from Amplify, which
has over 30 methods available (methods like signUp, signIn,
signOut, etc.), or you can use the framework-specific components
like withAuthenticator that will scaffold out an entire
authentication flow, complete with preconfigured UI. Let’s try out the
withAuthenticator higher-order (HOC) component.
First, configure the React app to work with Amplify. To do so, open
src/index.js and add the following code below the last import
statement:

```javascript 

``` 
