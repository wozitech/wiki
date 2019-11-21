---
title: 2019 All Day Dev Ops
description: 
published: true
date: 2019-11-21T11:47:16.711Z
tags: 2019 DevOps
---

<!-- TITLE: 2019 All Day Dev Ops -->

# All Recordings
https://www.alldaydevops.com/2019-live-schedule?utm_campaign=ADDO%202019&utm_source=hs_email&utm_medium=email&utm_content=79094693&_hsenc=p2ANqtz--XiYcDpPp_StAQip5Z1Of1XWFClXF-gCghvWnCFX7KOqytJx9YAXxgxhFjAmDBt3WEvtBi7hp6T9GZ_qj1VgnT-7rUKvueBb_d6ZvBT9Scc_iMTQE&_hsmi=79094518

## My Schedule
All Day DevOps Schedule

Wed 6th Nov
09:00 - Can Kubernetes keep a secret (Docker/Kubenetes) 
09:30 - Anatomy of a Continuous Delivey Pipeline (DevOps pipeline)
10:00 - What You See Is What you Get for AWS Infrastructure (Architecture)
~~10:30 - Event Driven Computing with Kubernetes (Docker/Kubenetes)~~ - *canncelled*
12:00 - OWASP Juice Shop: The Ultimate All Vuln WebApp (OWASP)
14:00 - Deploying Microservices to AWS Fargate (Archiecture)

Catch Up
Wed 6th Nov:
17:30 - Delivery Flexibility - Pipeline as Code with Jenkins & Groovy  (DevOps pipeline)
17:30 - Creating  stateful application with k8s and AWS DocumentDB and ElasticCache (Docker/Kubenetes)
18:00 - DevSecOps: Pipeline tooling for Continuous Security  (DevOps pipeline)
21:00 - OWASP AppSensor: Self-Defending Applications Through Real-Time Event Detection and Response (OWASP)

Thurs 7th Nov:
01:30 - OWASP Top 10 Overview (OWASP)
05:30 - Secure Your Kubernetes Containers (Docker/Kubenetes)


# Talks
Addidas CI/CD Piepline:
![2019 Addo Addiias Ci Cd Pipeline](/uploads/misc/2019-addo-addiias-ci-cd-pipeline.png "2019 Addo Addiias Ci Cd Pipeline")

## Can Kubernetes keep a secret (Docker/Kubenetes) 
"Super Devs" - write code, deploy to production and monitor/fix, but they need help:

Based on a [gitops](https://www.weave.works/blog/what-is-gitops-really) solution, it is easy to deploy into a k8s cluster, but how to pass secrets.

The kubernetes `manifest` can incldue secrets (kubernetes has a "secret" type  - but it is just base64). Can sign the secrets using gitops, but it means the secets is committed into git (because the `manifest` is a managed file).

Hashicorp Vault - has native kibernetes integration. The presenter did mention cloud alternative Azure KeyStore, and AWS Secret Manager. The presenter suggested that having secrets stored separately to their code created a problem (I don't agree myself).

He referenced Travis and signing secrets in travis build files - but travis is a deployment tool - which would replace gitops.

But came upon this open source [kamus](https://github.com/Soluto/kamus). Each pod (service) has an account token (JWT), which can be used with kamus, to allow for decrypting the given keys (the application deployed in the pod will w.

```
helm install kamus solutop/kamus
```

The open source project is looking to extend it's relevance beyond kubernetes.

## Anatomy of a Continuous Delivey Pipeline (DevOps pipeline)
Everyone needs:
* shorter release cycles
* continuous integration
* quality assurance
* security

Common comments:
* We have DevOps tools, but still not delivery fast enough
* To mych lead time from UAT to production
* Doing CI/CD but still can't release when they want
* We still see production issues

To get better, there are many aspects to consider:
![Aspects To Cover In Pipeline](/uploads/misc/aspects-to-cover-in-pipeline.png "Aspects To Cover In Pipeline")

**CD - Contunous Delivery - is to get your code ready to deploy and release at anytime**

Basic Pipeline:
* Develop
* Build - build on the platform you're going to run on - containerise your app
* Test
	* fail fast, fail early and fix early - unit tests (+80% coverage)
	* autpomate your tests - integration, UI, acceptance, security
* Deploy - infrastrcuture as code
* Release - rboust logging and monitoring

Summerised with:
![Pipeline Stages](/uploads/misc/pipeline-stages.png "Pipeline Stages")

> Totally disagree with the presenter saying to commit code often and that Pull Requests can be reviewed and approved by anyone; they're not for approval. This is non-sense as crap will so quickly get into the code.

The significance here is the "deploy/release" stage. Getting the pipeline to d3ev, build and test is well understood, and even being able to deploy into live. But the significance is the separation of production deployment and production release.

Deploying into production without actually releasing allows you to subsequently release at a future time whenever you want:.

Feature Tracking/Toggles" is esential:
* being able to deploy into production even if a feature has not been fully tested allows it to be subsequently switched one once tested
* but can also disable if it turns out the feature is not that good (without a redeployment!)

Extend this release approach further to have feature toggling/tracking not just on/off, but segmented by user location (IP address/country), user role/type. This allows new features to be easily trailed.

## What You See Is What you Get for AWS Infrastructure (Architecture)
By Anton Babenko - maintains the terraform aws modules! medium.com/@anton.babenko

** 3D style architecture visualisations - [cloudcraft.co](https://cloudcraft.co/) - free account forever**
Allows visualising regions, VPCs, security groups, auto-scaling groups, RDS et al, but also properties on them such as estimated costs and IAM roles.

[modules.tf](https://modules.tf/#!/)- integrated into cloudcraft, and exports the visualused infrastructure to terraform code, incorporates opinated best practice.
![Cloudcraft](/uploads/misc/cloudcraft.png "Cloudcraft")

[terragrunt](https://github.com/gruntwork-io/terragrunt) - orchestrate terraform configurations - where there are exclusive aspects of the deployment which can be done in parallel

## OWASP Juice Shop: The Ultimate All Vuln WebApp (OWASP)
This [Juice Shop](https://www2.owasp.org/www-project-juice-shop/#)  looks like any typical ecommerce shop; login  (demo/demo) to be able to add products to card. It's served locally on port 3000 (node/express/sequelize (with sequelite)/angular). And there is a full suite of reference tests in protractor, jasmine and mocha. Easy to download and run locally; but there are docker and vagrant images available.

This insecure app is great for testing:
![Juice Ship Categories](/uploads/misc/juice-ship-categories.png "Juice Ship Categories")

There are challenges for newbies and veterans alike, whereby you get to work through vulnerabilities to identify and fix. There is an "online instructor" to help beginners get started.

A great demo by the speaker showing SQL injection on login using `--` SQL comment and `true`.

Works with [CTFd](https://ctfd.io/about/); Some of the problems (CTF) requires starting the shop up with `NODE_ENV` variable.

Presenter has offered a free security lecture: https://github.com/bkimminich/it-security-lecture.

## Deploying Microservices to AWS Fargate (Archiecture)
Advantage of fargate over say ECS/EKS/kubernetes - not having to manage a cluster (such as node sizes and number of nodes), and certainly for own kubernates cluster, not having to patch the cluster. They did use AWS ECR registry for their docker images.

Fargate does not support updating running container images; this has been done external to the Fargate service.

Fargate runs within your own VPC; you own the networking, subs, NACLs and security groups.
Access is via Applicatoin Load Balancer with AWS WAF and SSL termination.


They were using git hooks to trigger `Jenkins Pipeline` to download a reference docimker image, checkout the code, build the new docker image with version number and then upload to AWS ECR. On every commit this is quite extreme, especially as there is no testing.

# Catch Up