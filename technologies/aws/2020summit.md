---
title: AWS Summit 20202
description: 
published: true
date: 2020-06-17T12:01:05.990Z
tags: 
---

# AWS Summit 2020

# Keynote
AWS to optimise private/local services or build large solutions. In light of current (Covid-19) events, the need to scale both up and down is essential. Having an online presence is essential, especially when you can't get to bricks and mortar premises, supply chain optimisation and fulfilment.

Everyone at AWS is able to benefit from day one; free tier and security built-in.

Well-Architecture framework is the go to place when crafting your projects/solutions.

![well-architected-framework.png](/uploads/aws/summit-2020/well-architected-framework.png)

Helps you to identify the AWS services of most relevance. Make use of the [Well Architecture Framework tool](https://aws.amazon.com/well-architected-tool/) to help you in your project journey. You can also use it for training - learn a new skill.

With current circumstances, more people are working from home. Video streaming services have exploded in use in recent weeks (more than 160 billion minutes in the USA for the month of March 2020 alone - double that in March 2019!). AWS scales with your demand; auto scale (up and down) meets your users expectations and your price limitations. AWS has introduced "predicted scaling" based on machine learning of your application.

What does a load balancer mean in 2020? Traditionally, a load balancer sits in front of your application to share load. But moreso now, CloudFront can provide regional optimisation, and redirecting to regional sites. Make more use of content distribution in addition to mor traditional load balancing.

Volume of data is forever increasing. Data generation, data processing and data storage. Amazon S3 scales to what you use only, with full ACL and events; S3 can be your "content lake" (data lake). Data in -> events -> Data out. Reference was made to film studios, using snowball locally to sync to S3, to then process and render film; processing where the data is closest by multiple people in multiple teams.

Referenced "Care Connect", a new application for video appointments with doctors in the US, maintaining access to vital healthcare CloudFront served by ELB & Fargate with Aurora. The app was launched in just over one week (surely Cognito for end user registration/security).

Referenced "nextdoor" service, connecting communtities with each other; like online readings clubs and teddy bear treasure hunts. Performance increase using ElasticCache, and data scaling with Aurora.

Referenced AWS DeepRacer online game/competition - dedicated to machine learning.

No new services or products announced. During event driven architecture breakout session, introduced `Eventbridge` and CloudWatch ServiceLens.


# Builder Fast Tracks

## Purpose-built databases for modern applications
10:00 -> 10:30 - Blair Leyton, Head of Database, APJ, Public Sector
Slides: 
[purpose-built_databases_for_modern_applications_-_blair_layton.pdf](/uploads/aws/summit-2020/purpose-built_databases_for_modern_applications_-_blair_layton.pdf)

![aws-database-types.png](/uploads/aws/summit-2020/aws-database-types.png)

Choose the right DB for the right task:
* Relational
* Key-Value
* Document
* In-Memory
* Graph
* Times-Series
* Ledger
* Wide Column


### Relational
Aurora
![aws-db-relational-aurora.png](/uploads/aws/summit-2020/aws-db-relational-aurora.png)

### Key/Value
Dynamo
![aws-db-keypair-dynamo.png](/uploads/aws/summit-2020/aws-db-keypair-dynamo.png)

![aws-db-keypair-dynamo-2.png](/uploads/aws/summit-2020/aws-db-keypair-dynamo-2.png)

### In-Memory
ElasticCache
![aws-db-in-memory-elasticcahe.png](/uploads/aws/summit-2020/aws-db-in-memory-elasticcahe.png)
  
### Graph
Neptune
![aws-db-graph-neptune.png](/uploads/aws/summit-2020/aws-db-graph-neptune.png)

### Time Series
timestream
![aws-db-timeseries-timestream.png](/uploads/aws/summit-2020/aws-db-timeseries-timestream.png)
  
### Ledger
Quantum
![aws-db-ledger-quantum.png](/uploads/aws/summit-2020/aws-db-ledger-quantum.png)

### Wide Column
Cassandra
![aws-db-wide-column-cassandra.png](/uploads/aws/summit-2020/aws-db-wide-column-cassandra.png)
  
### Our approach
![aws-db-our-approach.png](/uploads/aws/summit-2020/aws-db-our-approach.png)

## Full-stack mobile and web development with AWS Amplify
10:35 - 11:05
Marcia Villalba
Slides: [full-stack_mobile_and_web_development_with_aws_amplify_-_marcia_villalba.pdf](/uploads/aws/summit-2020/full-stack_mobile_and_web_development_with_aws_amplify_-_marcia_villalba.pdf)

AWS enables deveopers to exceed user expectations.

### Amplify
`Amplify` - an opinionated framework that supports most popular OS platforms and frameworks. iOS/Androd/webn - react and Angular.

It's a CLI tool, making it easy to connect to other AWS services, such as, Cognito.

Generates infrastructure as code; among the to p5 fastest growing projects on github.

It's a category based selection & customisation of services:
![aws-amplify-categories.png](/uploads/aws/summit-2020/aws-amplify-categories.png)

`init`, `add`, `mock`, `push`, `update` CLI commands, resulting in CloudFormation stacks:
![aws-amplify-commands.png](/uploads/aws/summit-2020/aws-amplify-commands.png)

For set of API libraries:
![aws-amplify-libraries.png](/uploads/aws/summit-2020/aws-amplify-libraries.png)

Amplify can be integrated with github for automated (managed) deployments.

### AppSync
![aws-appsync.png](/uploads/aws/summit-2020/aws-appsync.png)

AppSync is a GraphQL managed service. The GraphQL resolvers can be implemented in AWS Services (e.g. lambda) or private services through http (e.g. EKS microservices).

Support for offline availability on mobile clients.

AppSync supports transforms:
![aws-appsync-transforms.png](/uploads/aws/summit-2020/aws-appsync-transforms.png)
![aws-appsync-overview.png](/uploads/aws/summit-2020/aws-appsync-overview.png)

Can generate code `appsync codegen`.

## Application integration patterns for microservices
11:10 -> 11:40 Anshul Sharma
Slides: [application_integration_patterns_for_microservices_-_anshul_sharma.pdf](/uploads/aws/summit-2020/application_integration_patterns_for_microservices_-_anshul_sharma.pdf)

"Divide and conquer" with "loose not lousy coupling".

The latter (coupling) requires async messaging for being able to operate at scale.

Exchange scenarios:
  * One-way; fire and forget
  * Request-Response; requires a "return address" (callback) or "correlation id"
   
![aws-patterns-messasge-exchange.png](/uploads/aws/summit-2020/aws-patterns-messasge-exchange.png)

Channels:
* queue (point to point; resilient - only once)
* topic (pub/sub) - messages are bot stored by default, but can apply the "durable subscriber" pattern

Note - Messaging Quality of Service:
* At least One
* At most once
* Exactly Once

Patterns:
* Topic Queue Chaining
* DLQ - Dead Letter Queue
* FIFO
* Message Filter
* Saga - orchrestation of small activities through event sequences

## Event-driven architecture
11:55 -> 12:40 Danilo Poccia
Slides: [event-driven_architecture_-_danilo_poccia.pdf](/uploads/aws/summit-2020/event-driven_architecture_-_danilo_poccia.pdf)

Transition from APIs to events, using `EventBridge` and orchestration with `Step Functions`.

APIs are hardened contracts.

`API Gateway` allows for syandard authentication, CORS and trottling, leveraged with serverless functions (lambda).

* Use functions to transform, not transport.
* leave retry and error handling to the services themselves - not within the function.
* Read only what you need; message filters using SNS, fine grained rules in EventBridge, query filters in RDS, S3 select.

### EventBridge (Event Bus)
An event is "when something happens"; it's a fact. An event is immutable; once an event happens, you can't change the event. If "something" changes a previous event, it is a new event.

![aws-event-drive-arch-events.png](/uploads/aws/summit-2020/aws-event-drive-arch-events.png)

AWS Lambda now supports "success/failure event destinations"; a consequential event:
![aws-event-drive-arch-lambda-event-dest.png](/uploads/aws/summit-2020/aws-event-drive-arch-lambda-event-dest.png)

SQS/SNS have limitation with the significant number of different types of events. Introducing the EventBridge - a rule based solution for event routing:
![aws-event-drive-arch-eventbridge.png](/uploads/aws/summit-2020/aws-event-drive-arch-eventbridge.png)

External partners can integrate their products with EventBridge, to open up their application services - push style APIs.

Supports a schema registry with discovery, for events.

Sometimes an event may require multiple actions; when those actions need to be coordinated across multiple functions/services, you can deploy "step functions".

Event tracing is provided through X-Ray; which integrates directly with API Gateway and Lambda, with the introudction of a correlation id. That can be extended into the EventBridge to trace single events.

CloudWatch Servicelens is a one-stop-shop for all traces, metrics and logs.

CloudWatch Embedded Metrics - can be used to instrument code, to introduce monitoring dimensions.

## A path to event sourcing with Amazon MSK
12:45 to 13:15 - James Ousby
Slides: [a_path_to_event_sourcing_with_amazon_msk_-_james_ousby.pdf](/uploads/aws/summit-2020/a_path_to_event_sourcing_with_amazon_msk_-_james_ousby.pdf)

* Eventing sourcing is not persisting entities and updating that entity.
* Event sourcing is about a timeline of events, taking action on an entity.

Event sourcing allows replay from any point in time (for test purposes or to correct a corruption, or support evolution by replay history through new logic), in addition to a natural undo (replay the reverse of the event in reverse order).

Close friends:
* Domain Driven Design
* CQRS - Command Query Responsibility Segregation - separates read and write paths
* Distributed Logs (ergo kafka)

With events, there is a theory that you should capture all raw data in a data lake, allowing that data to be reprocessed at any time in the future.

Talk example:
![aws-event-sourcing-cqrs.png](/uploads/aws/summit-2020/aws-event-sourcing-cqrs.png)

Event sourcing concept has been around for a long time; I remember it was a key aspect while at Photobox. There is a lack of framework support, and it can be more complex with more moving parts. IT has for many years been about functionality of which data was a consequence (or a pain to be managed), but of late is more about data.

AWS MSK takes care of setup, availability, scale, monitoring and support for complex kafka clusters.
![aws-event-sourcing-msk.png](/uploads/aws/summit-2020/aws-event-sourcing-msk.png)

AWS MSK now also supports prometheus for monitoring, allowing integration via Datadog and New Relic.