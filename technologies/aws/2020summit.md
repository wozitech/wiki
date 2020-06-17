---
title: AWS Summit 20202
description: 
published: true
date: 2020-06-17T09:13:17.318Z
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

No new services or products announced.


# Builder Fast Tracks

## Purpose-built databases for modern applications
10:00 -> 10:30
<insert image>

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

<insert image>

Slides: 
[purpose-built_databases_for_modern_applications_-_blair_layton.pdf](/uploads/aws/summit-2020/purpose-built_databases_for_modern_applications_-_blair_layton.pdf)

## Full-stack mobile and web development with AWS Amplify
10:35 - 11:05

## Application integration patterns for microservices
11:10 -> 11;40

## Event-driven architecture
11:55 -> 12:40

## A path to event sourcing with Amazon MSK
12:45 to 13:15