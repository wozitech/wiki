<!-- TITLE: WOZiTech CMS -->
<!-- SUBTITLE: Uses strapi - headless CMS with strong APIs yet a good Admin UI -->

# Repo
https://github.com/wozitech/strapi.git

# Decompose
WOZiTech CMS is built on `strapi`:
```
npm install -g strapi
strapi new wozitech-cv
```

This creates a new strapi CMS application, using a wizard to guide through the customisation, namely, to select "MongoDB" as the backend store with the connection details to that database (use a local MongoDB instance first - available on localhost:27001).

It then continues to builds the Angular UI and then starts up the application, launching a web browser to complete the installation by providing the initial admin username and password (go for a non-descript username and password - i used `admin/password`).

You can then add the project to a newly created github repo; and there you have you initial strapi application:
1. github repo
2. local MongoDB database

[How strapi Works](projects/cms/strapi)
# Develop and Deploy
strapi provides alot of the functionality out of the box, but there are times when it will be necessary to custom strapi:
* Trigger on local updates (new, change and delete) to custom custom to update microservices

Consequently, need to be able to develop such features, commit to github repo, and then deploy for testing and deploy to production.

## Custom Content Types
Content types can be created on any instance, but ideally, we want to manage the content types across environments of local dev, local test, deployed test and deployed production - to ensure the content types are consistent.

## Custom Content Type Triggers
Triggers can be registered against custom Content Types, allowing the before/after new, update and delete events to be captured to allow the subsequential RESTful update via microservices.

# Build, Deploy and Test
Deploy is the local dev/test environment, showing how the WOZiTech customised strapi application is:
![Wozitech Cms Test](/uploads/projects/wozitech-cms-test.png "Wozitech Cms Test")

1. cloned from guthub
2. built as a container, using a reference image (to be decided) onto which node.js and pm2 is installed
	3. Note  - pm2 with node.js dockerhub image: https://hub.docker.com/r/keymetrics/pm2/ - uses only the formal releases of node.js
4. The resulting docker image registered in local sonatype Nexus
5. docker swarm used to deploy locally (includes running mongodb container for database) - this is to allows for interface testing of the container image, which includes being able to test for old and new custom content types, and managing the mongoDB reference backup image for system testing
	6. terraform is overkill for local deployment, but the deployment template can be reused for kubernetes deployment and subsequently AWS ECS deployment.
6. terraform used to deploy locally to k8s - at least two CMS app instances (pods) and as shared MongoDB app instance (pod).
	7. Used for integration and acceptance testing.
	8. The test environment is created from scratch everytime.The mongodb ref database imported during the pod creation from a reference backup in github.
	