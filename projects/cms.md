<!-- TITLE: WOZiTech CMS -->
<!-- SUBTITLE: Uses strapi - aeadless CMS with strong APIs yet a good Admin UI -->

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