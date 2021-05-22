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




