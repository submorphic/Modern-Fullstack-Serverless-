
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

