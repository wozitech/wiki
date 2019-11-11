<!-- TITLE: WOZiTech CV -->
<!-- SUBTITLE: A refactoring of online resume to make more interactive, separate data from presentation, with data served via GraphQL across a set of microservices -->

This is a revitalisation (mdoernisation) of my online resume - by using a microservice/GraphQL backend against a Vue.JS client application.

## CMS
All resume content is store in strapi (CMS): https://github.com/wozitech/strapi.git. WOZiTech CMS instance, which will be hosted on home server as as docker image - initially a docker instance on a KMS server, but ultimately deployed to kubernetes with data stored in MongoDB Atlas database. Accessed via the `wozitech.asuscomm.com` DDNS by the URL https://wozitech.asuscomm.com/cms.

Custom Content types for:
* Career
* Training
* Skills
* Profesionalism

The GET/LIST API endpoints on each of the above content types for public access.

Atlas Backups will be enabled.

Insomnia Project - across all the APIs, including showing the filtering, ordering and pagination on the skills data.

## Frontend/UI
VueJS web application; the only fix content will be the profile. [UI](/projects/cv/ui) - the Vue.JS frontend - uses material UI components

All other data via external APIs. 

Only the profile, career history and professional certifications/bodies will be shown by default. And the career history will only be the last three roles - with reveal to pull back the rest of career history.

Separate tabs for training and skills. With skills, should allow for filtering, order and pagination and ideally facetted search.

### S3/Circle CI
The Vue.JS application is built via Circle CI and deployed to the S3 - so whenver changes are committed (fully local testing), all changes are deployed (no more manual S3 uploads and remembering to make public). This includes generating the target API reference domain.

# Initial Solution
The initial solution will be for WOZiTech CV to consume content direct from WOZiTech CMS:
![Wozitech Cv Initial Solution](/uploads/cv/wozitech-cv-initial-solution.png "Wozitech Cv Initial Solution")

# Target Solution
With the initial solution in place, each of the strapi custom type APIs will be refactored to individual microservices. strapi, will trigger on updates (new content, changed content and deleted) content and will invoke with JWT the microservice `POST`, `PUT` and `DELETE` endpoints to ensure the data knwon to the microservice is relevant. The micrsoervice when starting up, if no data, will pull all data from WOZiTech CMS.
![Wozitech Cv Target Solution](/uploads/cv/wozitech-cv-target-solution.png "Wozitech Cv Target Solution")

`terraform` will be used for all the AWS configuration, including RDS, ECS and API Gateway with all necessary IAM.
`Circle CI` will be used to build (ECR) and deploy (ECS) the containers, for each microservice change that has passed acceptance testing.

Note:
* API Gateway is used to access the ECS services (this is a better serverless solution compared to the more traditional ELB/ECS - but moreso, API Gateway as an always free tier - whereas their is no free tier on ELB).
* API Gateway supports custom domain on APIs that can then be used as a Route 53 alias (CNAMES). But to do so, it has to create a CloudFront distribution and CloudFront is only available for the first 12 months as free tier. Hence, the WOZiTech CV UI uses the AWS API Gateway provided URL

All microservices must run locaolly - no runtime. `Docker Swarm` will be used to run a single instance of each microservice locally - allowing for Interface and Integration tests. `kubernetes` will be used locally for integration, accpetance and load tests.

## The Microservices
* [training](/projects/cv/training) - a python RESTful API endpoint using flask and connexion against a postgres data store - to be deployed locally, by serverless (lambda) and docker
* [career](/projects/cv/career) - a nest.js RESTful API endpoint against MongoDB data store  - to be deployed locally, by serverless (lambda) and docker
* [professional](/projects/cv/professional) - a Golang RESTful API endpoint against a postgres data store - to be deployed locally, by serverless (lambda) and docker
* [skills](/projects/cv/skills) - a C#.NET RESTful API - to be deployed locally, by serverless (lambda) and docker
* TBC - [skills]() - a Java RESTful API - to be deployed locally, by serverless (lambda) and docker

## Serverless
Not shown, but should also consider showing how this could run with serverless (lambda) - this is especially true given the low performance requirements. Support a mixed mode environment - AWS API GW allows for a single endpoint, but both ECS and lambda.

## Tests
Each microservice:
* Static Code Stats
* Unit tests - 100% coverage
* Interface tests - jest with supertest and includes error code

Across all deployed microservices:
* Integration tests - jest with supertest - covers only expected behaviour

Across all deployed microservices and UI:
* Acceptance test - protractor (and possibl cumcumber for BDD (user story) style test cases)
* Load Testing - TBC

Across the deployed (production) services:
* Load Testing - TBC