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

You can access the schema via the "Public" Playground, after you have logged into your account.There you can see the schema with relevant information, you can test queries and mutations, etc.

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
mutation createPartnerVideoRoom($input: CreatePartnerVideoRoomInput){
    createPartnerVideoRoom(input: $input){
        uid
        startTime
        timeZone
        displayName
        patientLink
        providerLink
        patientDuration
        providerDuration
        didPatientAttend
        didProviderAttend
    }

```

2. `partnerVideoRoom(uid: String!): PartnerVideoRoom`

Here is an example query:

```graphql
query partnerVideoRoom($uid: ID!){
    partnerVideoRoom(uid: $uid){
        uid
        startTime
        timeZone
        displayName
        patientLink
        providerLink
        patientDuration
        providerDuration
        didPatientAttend
        didProviderAttend
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

