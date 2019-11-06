<!-- TITLE: 2019 All Day Dev Ops -->

# Schedule
https://www.alldaydevops.com/2019-live-schedule?utm_campaign=ADDO%202019&utm_source=hs_automation&utm_medium=email&utm_content=71598814&_hsenc=p2ANqtz-81-Orl8lKBhfheas5cR-3iHMjIDfeBcEYKx4q8ORDMt_0CfL-hhdmxa59GWXFQmcDQ7G5flVZJIg5E0VAnTIYN5ODR5IZ-AWMHiE78C4AJ9wdPDUQ&_hsmi=71598814

## My Schedule
All Day DevOps Schedule

Wed 6th Nov
09:00 - Can Kubernetes keep a secret (Docker/Kubenetes) 
09:30 - Anatomy of a Continuous Delivey Pipeline (DevOps pipeline)
10:00 - What you see if what you Get for AWS Infrastructure (Architecture)
10:30 - Event Driven Computing with Kubernetes (Docker/Kubenetes)
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

Based on a gitops solution, it is easy to deploy into a k8s cluster, but how to pass secrets.

The kubernetes `manifest` can incldue secrets (kubernetes has a "secret" type  - but it is just base64). Can sign the secrets using gitops, but it means the secets is committed into git (because the `manifest` is a managed file).

Hashicorp Vault - has native kibernetes integration. The presenter did mention cloud alternative Azure KeyStore, and AWS Secret Manager. The presenter suggested that having secrets stored separately to their code created a problem (I don't agree myself).

He referenced Travis and signing secrets in travis build files - but travis is a deployment tool - which would replace gitops.

But came upon this open source [kamus](https://github.com/Soluto/kamus). Each pod (service) has an account token (JWT), which can be used with kamus, to allow for decrypting the given keys (the application deployed in the pod will w.

```
helm install kamus solutop/kamus
```

The open source project is looking to extend it's relevance beyond kubernetes.

## Anatomy of a Continuous Delivey Pipeline (DevOps pipeline)

## What you see if what you Get for AWS Infrastructure (Architecture)

## Event Driven Computing with Kubernetes (Docker/Kubenetes)

## OWASP Juice Shop: The Ultimate All Vuln WebApp (OWASP)

## Deploying Microservices to AWS Fargate (Archiecture)

# Catch Up