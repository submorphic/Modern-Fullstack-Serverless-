
# Chapter 9, Building Offline Apps with Amplify DataStore

Amplify supports another type of API for interacting with AppSync: Amplify DataStore. DataStore has a different approach than a traditional GraphQL API.

Instead of interacting with the GraphQL API itself, using queries and mutations, DataStore introduces a client-side SDK that allows you to write to and read from a local store and persists this data locally using the local storage engine of the platform you are working with (i.e., IndexDB for web and SQLite for native iOS and Android).

DataStore then automatically syncs the local data to the GraphQL backend for you as updates are made both locally and remotely.

