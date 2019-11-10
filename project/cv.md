<!-- TITLE: WOZiTech CV -->
<!-- SUBTITLE: A refactoring of online resume to make more interactive, separate data from presentation, with data served via GraphQL across a set of microservices -->

This is a revitalisation (mdoernisation) of my online resume - by using a microservice/GraphQL backend against a Vue.JS client application.

## CMS
All resume content is store in strapi (CMS): https://github.com/wozitech/strapi.git. WOZiTech CMS instance, which will be hosted on home server as as docker image - initially a docker instance on a KMS server, but ultimately deployed to kubernetes with data stored in MongoDB Atlas database.

## Frontend
VueJS web application; the only fix content will be the profile. [UI](/projects/cv/ui) - the Vue.JS frontend - uses material UI components

All other data via external APIs. 

Only the profile, career history and professional certifications/bodies will be shown by default. And the career history will only be the last three roles - with reveal to pull back the rest of career history.

Separate tabs for training and skills. With skills, should allow for filtering, order and pagination and ideally facetted search.

# Initial Solution
The initial solution will be for WOZiTech CV to consume content direct from WOZiTech CMS.


# Target Solution

## The Microservices
* [training](/projects/cv/training) - a python RESTful API endpoint using flask and connexion against a postgres data store - to be deployed locally, by serverless (lambda) and docker
* [career](/projects/cv/career) - a nest.js RESTful API endpoint against MongoDB data store  - to be deployed locally, by serverless (lambda) and docker
* [professional](/projects/cv/professional) - a Golang RESTful API endpoint against a postgres data store - to be deployed locally, by serverless (lambda) and docker
* [skills](/projects/cv/skills) - a C#.NET RESTful API - to be deployed locally, by serverless (lambda) and docker

## Tests
Each microservice:
* Static Code Stats
* Unit tests - 100% coverage
* Interface tests - jest with supertest and includes error code

Across all deployed microservices:
* Integration tests - jest with supertest - covers only expected behaviour

Across all deployed microservices and UI:
* Acceptance test - protractor (and possibl cumcumber for BDD (user story) style test cases)