---
draft: false 
date: 2024-11-03
categories:
  - Swift 
  - GRPC
---


We will create a grpc server in swift and access it from a go client.

<!-- more -->

We will track what the guy in the [blog post on medium](https://medium.com/@sergiocampama/tutorial-type-safe-apis-with-swift-grpc-dc4b0e4045ae) says.


``` 
swift package init
```

create the `.proto` file

```
brew install protobuf
```

```
brew install grpc-swift
```

its a bit too complex to implement a metal api library, without good understanding of swift.

Postponing it for later