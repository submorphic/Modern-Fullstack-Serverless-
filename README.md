# Modern-Fullstack-Serverless

Notes from Book: 
Full Stack Serverless: Modern Application Development with React, AWS, and GraphQL 

By:
Nader Dabit 

----------------------------------------------------------------------------

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




