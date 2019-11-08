<!-- TITLE: WOZiTech CV -->
<!-- SUBTITLE: A refactoring of online resume to make more interactive, separate data from presentation, with data served via GraphQL across a set of microservices -->

This is a revitalisation (mdoernisation) of my online resume - by using a microservice/GraphQL backend against a Vue.JS client application.

# The Components
* [UI](/projects/cv/ui) - the Vue.JS frontend - uses material UI components
* [training](/projects/cv/training) - a python RESTful API endpoint using flask and connexion against a postgres data store - to be deployed locally, by serverless (lambda) and docker
* [career](/projects/cv/career) - a nest.js RESTful API endpoint against MongoDB data store  - to be deployed locally, by serverless (lambda) and docker
* [professional](/projects/cv/professional) - a Golang RESTful API endpoint against a postgres data store - to be deployed locally, by serverless (lambda) and docker
* [skills](/projects/cv/skills) - a C#.NET RESTful API - to be deployed locally, by serverless (lambda) and docker
