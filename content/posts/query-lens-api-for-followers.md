+++
authors = ["vibern"]
title = "Query Lens API (get followers)"
date = "2024-03-25"
description = "How to query lens api to get followers of an account"
tags = [
    "lens",
    "api",
    "ethereum",
]
categories = [
    "web3",
    "lens",
]
series = ["lens"]
+++


Suppose you want to do something with Lens Protocol and that requires getting the followers of a given account.

Head over to https://api-v2.lens.dev

You will default with an UI to an apollo server. Pretty well documented. On the left, you will have the query fields. The easiest way to interact is just to add new queries using that. To get the followers of an account, you will first need the `profileId`. And to get that, you need the address. With that you need to call the `defaultProfile` with that address. The Operation and the Variables will look like the following, respectively.

```graphql
query ExampleQuery($defaultProfileRequest: DefaultProfileRequest!) {
  defaultProfile(request: $defaultProfileRequest) {
    id
  }
}
```
```json
{
  "defaultProfileRequest": {
    "for": "0xA6b94Ce98D6CD4f447a9C6788F169DD17f65f747"
  }
}
```

With this you get the `profileId`, which is something like `0x03683c`. To get the followers of that profile, you now request the `followers`. Again, the Operation and the Variables will look like the following, respectively.

```graphql
query ExampleQuery($followersRequest: FollowersRequest!) {
  followers(request: $followersRequest) {
    items {
      id
    }
  }
}
```
```json
{
  "followersRequest": {
    "of": "0x03683c"
  }
}
```

That's it. In this example, I'm using my account, which at the time of writting, does not have much activity yet. You can look for others with more activity, like `0x7aa96bFCa841B8FC3879c17555330CBDBbCAD3e8`.

One extra, to get the list of profiles the user follows, use `following`. Once again, the Operation and the Variables will look like the following, respectively.

```graphql
query ExampleQuery($followingRequest: FollowingRequest!) {
  following(request: $followingRequest) {
    items {
      id
    }
  }
}
```
```json
{
  "followingRequest": {
    "for": "0x03683c"
  }
}
```