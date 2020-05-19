---
description: All about BetterPT's GraphQL API
---

# GraphQL API

## Overview

BetterPT's next-generation API is built on top of [GraphQL](https://graphql.org/). It is fast, type-safe, self-documenting and flexible. Plus, all of the cool kids and hipsters are using it. We really dig it.

However we also realize that not everyone has used GraphQL before. The learning curve on the client side is quite gentle. Here are a few resources to get started:

* [GraphQL Home](https://graphql.org)
* [GraphQL Clients](https://graphql.org/code/#graphql-clients)

Recommended Clients

* [C\#/.NET](https://chillicream.com/blog/2019/11/25/strawberry-shake_2)
* [Java/Scala](https://github.com/americanexpress/nodes)
* [node.js](https://github.com/prisma-labs/graphql-request)
* [React.js, Angular, Vue](https://www.apollographql.com/docs/react/)

## The Schema

Below is the schema for the API. Normally you would be able to access this via the playground, but it is a protected endpoint. We are working on a solution for this.

```graphql
scalar Date


#  A RFC 3339 compliant date/time scalar type. Example: 2020-12-03T10:15:30Z. 
""" A RFC 3339 compliant date/time scalar type. Example: 2020-12-03T10:15:30Z. """
scalar DateTime


#  Handles pagination 
""" Handles pagination """
type ListPager {
  limit: Int
  offset: Int
  count: Int
  total: Int
}

enum PartnerVideoRoomTimeZonesEnum {
  America_Adak
  America_Anchorage
  America_Boise
  America_Chicago
  America_Dawson
  America_Dawson_Creek
  America_Denver
  America_Detroit
  America_Indiana_Indianapolis
  America_Indiana_Knox
  America_Indiana_Marengo
  America_Indiana_Petersburg
  America_Indiana_Tell_City
  America_Indiana_Vevay
  America_Indiana_Vincennes
  America_Indiana_Winamac
  America_Indianapolis
  America_Juneau
  America_Kentucky_Louisville
  America_Kentucky_Monticello
  America_Knox_IN
  America_Los_Angeles
  America_Louisville
  America_New_York
  America_Nome
  America_North_Dakota_Beulah
  America_North_Dakota_Center
  America_North_Dakota_New_Salem
  America_Phoenix
  America_Sitka
  America_Vancouver
  America_Yakutat
}


# A PartnerVideoRoom contains all necessaey data for a telehealth session.
"""
A PartnerVideoRoom contains all necessaey data for a telehealth session.
"""
type PartnerVideoRoom {
  uid: ID!
  startTime: DateTime!
  timeZone: String!
  displayName: String
  patientLink: String!
  providerLink: String!
  patientDuration: Int
  providerDuration: Int
  didPatientAttend: Boolean
  didProviderAttend: Boolean
}


# The input type for the `createPartnerVideoRoom` mutation.
"""
The input type for the `createPartnerVideoRoom` mutation.
"""
input CreatePartnerVideoRoomInput {
  startTime: DateTime!
  timeZone: PartnerVideoRoomTimeZonesEnum!
  displayName: String
}

type Mutation {
  
  # Creates a `PartnerVideoRoom`
  """
  Creates a `PartnerVideoRoom`
  """
  createPartnerVideoRoom(input: CreatePartnerVideoRoomInput!): PartnerVideoRoom!
}

type Query {
  
  # View details for a `PartnerVideoRoom`. Duration and attendance will be updated 2 hours after startTime.
  """
  View details for a `PartnerVideoRoom`. Duration and attendance will be updated 2 hours after startTime.
  """
  partnerVideoRoom(uid: ID!): PartnerVideoRoom!
}

schema {
  query: Query
  mutation: Mutation
}
```

## The Playground

The best place to get started with BetterPT's API is to make some test queries and mutations in the GraphQL Playground.

Visit [https://api-staging-k8s.betterpt.com/v0/graphql-public](https://api-staging-k8s.betterpt.com/v0/graphql-public) and you will see the following:

![At the playground.](.gitbook/assets/image-2020-04-10-at-15.18.35.png)

{% hint style="info" %}
Please make sure that the url is the same in BOTH the top browser URL bar and the bottom playground URL bar.
{% endhint %}

## Queries and Mutations

Because we are only interested in creating video rooms, the operations that matter to us are:

1. `createPartnerVideoRoom(...): PartnerVideoRoom`

Here is an example mutation:

```graphql
mutation {
  createPartnerVideoRoom(input: { startTime: "2020-12-03T10:15:30Z", displayName: "Bob the PT", timeZone: "America/New_York" } ) {
    patientLink
    providerLink
    startTime
    displayName
    timeZone
    uid
  }
}
```

        And here is an example response:

```javascript
{
  "data": {
    "createPartnerVideoRoom": {
      "patientLink": "https://meet-staging.betterpt.com/room/09c84a60-8367-4687-beae-ab6b647372c9?code=5073ade4-8b4b-4252-8b78-2718fc8d1328&type=patient",
      "providerLink": "https://meet-staging.betterpt.com/room/09c84a60-8367-4687-beae-ab6b647372c9?code=3f10184d-03fa-4657-a6bd-a0639d3a3c8e&type=provider",
      "startTime": "2020-12-03T10:15:30.000Z",
      "displayName": "Bob the PT",
      "timeZone": "America/New_York",
      "uid": "09c84a60-8367-4687-beae-ab6b647372c9"
    }
  },
  "extensions": {
    "cacheControl": {
      "version": 1,
      "hints": []
    }
  }
}
```

This is the "raw response", however many GraphQL clients return the `data` block automatically so there isn't a need to parse. Note that we are using a [GraphQL Input type](https://graphql.org/learn/schema/#input-types) in this mutation.

### Example

Below is a full example of a mutation in node.js & TypeScript. `graphql-request` is a lower-level library with little abstraction built-in -- it is perfect for a machine to machine request. There are similar libraries available for many other languages. **Note that this does not include fetching a valid token.**

```typescript
import { GraphQLClient } from 'graphql-request'


export const mutateVideoRoom = async (token: string): Promise<any> => {
  const endpoint = 'https://api-staging-k8s.betterpt.com/v0/graphql-public';
  const graphQLClient = new GraphQLClient(endpoint, {
    headers: {
      Authorization: `Bearer ${token}`,
    },
  });
  const mutation = `
  mutation {
      createPartnerVideoRoom(input: { startTime: "2020-12-03T10:15:30Z", displayName: "Bob the PT", timeZone: "America/New_York" } ) {
        patientLink
        providerLink
        startTime
        displayName
        timeZone
        uid
      }
    }
  `;
  const data = await graphQLClient.request(mutation);
  return data;
};
```

2. `partnerVideoRoom(uid: String!): PartnerVideoRoom`

Here is an example query:

```typescript
const querySession = async (token: string): Promise<any> => {

  const endpoint = 'https://api-staging-k8s.betterpt.com/v0/graphql-public';
  const client = new GraphQLClient(endpoint, {
    headers: {
      Authorization: `Bearer ${token}`,
    },
  });
  
  const query = `
    query {
        partnerVideoRoom(uid: "6f8fa1d7-430f-4c04-9405-fe95cbc7efad") {
          displayName
          didPatientAttend
          didProviderAttend
          patientDuration
          providerDuration
          startTime
        }
      }
  `;
  const data = await client.request(query);
  return data;
}
```

And the response \(note that this does not contain the metadata from the example response above\):

```typescript
{
  partnerVideoRoom: {
    displayName: 'Bob the PT',
    didPatientAttend: false,
    didProviderAttend: false,
    patientDuration: null,
    providerDuration: null,
    startTime: '2020-12-12T10:15:00.000Z'
  }
}
```

## Using the data in real life

All of this GraphQL stuff has been super-fun and I would love to write more about it \(I really would, I'm a bit obsessed with GraphQL\) but how do we use this data in the real world? The response provides the following:

1. Two links that are clearly marked as for the provider or for the patient.
2. The time zone.
3. The "display name" of the PT - this is displayed in the video interface.
4. The uid.

In order to start a session:

1. The patient opens the patient link, the provider opens the provider link.
2. Either user can start the session \(for now\).

**The session is open for 60 minutes and users can enter five minutes before the start time.**

## Other Information

{% hint style="danger" %}
This section will be available soon.
{% endhint %}

