<!-- TITLE: WOZiTech CMS -->
<!-- SUBTITLE: Uses strapi - aeadless CMS with strong APIs yet a good Admin UI -->

# Repo
https://github.com/wozitech/strapi.git

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


1. cloned from guthub
2. built as a container, using a reference image (to be decided) onto which node.js and pm2 is installed
	3. Note  - pm2 with node.js dockerhub image: https://hub.docker.com/r/keymetrics/pm2/ - uses only the formal releases of node.js
4. The resulting docker image registered in local sonatype Nexus
5. terraform used to deploy locally (to docker runtime - assumes an already running docker mongo container for database) - this is to allows for interface testing of the container image
	6. terraform is overkill for local deployment, but the deployment template can be reused for kubernetes deployment and subsequently AWS ECS deployment.
6. terraform used to deploy locally to k8s - at least two CMS app instances (pods) and as shared MongoDB app instance (pod).
	7. Used for integration and acceptance testing.