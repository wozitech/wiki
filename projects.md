![Wiki Official Blue Circle](/uploads/corporate/wiki-official-blue-circle.png "Wiki Official Blue Circle"){.pagelogo}
<!-- TITLE: Projects -->

WOZiTech Projects are presented on [WOZiTech Company Website](http://www.wozitech-ltd.co.uk/projects.html).
# Reference
WOZ*iTech* reference microservice (AWS lambda/APIGW node.js project: https://github.com/wozitech/microservice-reference.

To duplicate the reference repo, where the target repo name is `new-repo` (**THIS REPO MUST ALREADY EXIST):
```
git clone --bare https://github.com/wozitech/microservice-reference.git
cd microservice-reference.git
git push --mirror https://github.com/wozitech/new-repo.git
cd ..
rm -rf microservice-reference.git
```

Now customise the repo:
1. `README.md`
2. `package.json`:
	2.1. `name` and `description`
	2.2. `repository.url`
3. `serverless.yml`
	3.1. `service`
	3.2. (optional) `custom.bucketName` (if using S3 to as backend data store)
	3.3. (optional) `custom.region`
	3.4. (optional): `provider.profile`
	3.5. (add): `provider.role` (to a known IAM role)
	3.6. (add) `provider.timeout` and `provider.memorySize`
	
# Nexus
Available on https://wozitech.asuscomm.com:4431 - both the UI and the docker repository.

[Nexus Build](/projects/nexus)

Have a local Nexus server, using nginx reverse SSL proxy coming off proxy context path `/nexus`. To get this to work, have to set the Nexus context path to `/nexus` too.

Then to create and use nexus as a docker repo: https://blog.sonatype.com/using-nexus-3-as-your-repository-part-3-docker-images.

When creating Nexus docker repos, in addition to the Nexus base URL, each repo can have a dedicated listener. On my Nexus instance, port 9080 is for the hosted docker repo (private) and port 9081 is for the proxy docker repo. The group docker repo (includes hosted and proxy) has no dedicated port.

Excited as I was to get this configuration working (I could login to the Nexus admin UI using https via the Extranet proxy with SSL, the `docker` client is not able to support docker repo with a context root other than `/`: https://support.sonatype.com/hc/en-us/articles/115013153887-Docker-Repository-Configuration-and-Client-Connection. This is forcing me to map ports through the reverse proxy to create the necessary SSL connection to the individual repo http connector ports.

Also, it is not expected that you can push to a group repo, and must instead opt to have the dedicated port (http connector) to push against. However,  this article shows a little nginx proxy rewrite to map GET requests to the group repository (called just docker in his case) and all PUT/POT/PATCH et al methods to the hosted repository, thus effectively looking like you're getting and pushing from the same repo: https://stackoverflow.com/questions/47178055/nexus3-push-to-docker-group-repo.




## WOZiTech DEV LAN
From hosts on the DEV LAN, the firewall allows direct passthrough to the Nexus server (x.x.x.32)  on ENTERPRISE LAN. These LANs are isolated from Internet/wireless and therefore password security credentials without SSL OK.

Create (or update) `/etc/docker/daemon.json` and restart docker services `systemctl restart docker`:
```
{
  "insecure-registries": [
    "10.0.0.32:9080",
    "10.0.0.32:9081"
  ]
}
```

Note - these hosts/ports must be available for docker to start correctly.

Must then login to Nexus repostiroy:
```
docker login --username aylingw http://10.0.0.32:9080
docker login --username aylingw http://10.0.0.32:9081
```

This will create entries in `~/.docker/config.json`, e.g.:
```
{
	"auths": {
		"10.0.0.32:9080": {
			"auth": "YXlsaW5ndzpRdWluaWUwMA=="
		},
		"10.0.0.32:9081": {
			"auth": "YXlsaW5ndzpRdWluaWUwMA=="
		},
		"wozitech.asuscomm.com": {
			"auth": "YXlsaW5ndzpRdWluaWUwMA=="
		}
	},
	"HttpHeaders": {
		"User-Agent": "Docker-Client/19.03.2 (linux)"
	}
}
```

What is important to note for then working with docker commands is the "auth" name, e.g. `10.0.0.32:9080`  - no http or https prefix.

To pull an DockerHub image (use the proxy port - ergo 9081 in my case: `docker pull 10.0.0.32:9081/httpd:2.4-alpine`.

To search on Docker Hub (use the proxy port): `docker search 10.0.0.32:9081/httpd:2.4`.

To push to Docker hosted repo ((assumes you have a local imaged tagged as `wozitech/wozitech-cms`):
```
docker tag wozitech/wozitech-cms 192.168.1.201:8083/wozitech-cms
docker push 192.168.1.201:8083/wozitech-cms
```

## Home LAN 
Passing user ceredentials without SSL over the home network (wifi) or externally via Internet is a no go.

> As a temporarily solution, to prove uploading private images, have mapped 192.168.1.201:8083 to the Nexus docker hosted repository, and on my dev PC have added as an insecure registry.

Build the docker image locally, e.g. `docker build -t wozitech/wozitech-cms .`, in the local docker repository (`docker image ls`) is an image called `wozitech/wozitech-cms`.

To upload (where the docker image name is `wozitech-cms`:
```
docker login --username aylingw http://192.168.1.201:8083
docker tag wozitech/wozitech-cms 192.168.1.201:8083/wozitech-cms
docker push 192.168.1.201:8083/wozitech-cms
```

The `docker tag` command tags your local image with the target repo name (do a `docker image ls` to prove it). The `docker push`  command then forces the local image to the remote repo.
# CMS
Whilst working on SFC project at Sopra Steria, I did some evaluations on an API driven CMS; out of four contenders, [strapi](https://strapi.io/) came out the strongest for APIs and it supports MongoDB Atlas - which opens up the realms of streams an, stitch and the rest of the AWS set of services for integration and workflow. The admin UI was good, although pending a feature to add ACLs within the UI.

Having a [WOZiTech CMS](/projects/cms) with RESTful APIs is a good place to prototyping new applications.

# CV Project
 Warren Ayling resume took SPA to the extreme resulting in a very large `index.html` to my profile, career, professional memberings, certifications, acheivements, skills and competencies all listed in one file. It required HTML formatting on each block of data.
 
 The [CV](/project/cv) project is a modernisiatoin of Warren Ayling online resume. It separates the data from the presentation.
# WIT Projects
* [wit-myBuses](/projects/wit-myBuses) - an Alexa skill to tell me when my next bus is for given locations
* [wit-domain](/projects/wit-domain)
* [wit-myDash](/projects/wit-myDash) - flagship content display app
* [wit-serve](/projects/wit-serve)
* [wit-pi](/projects/wit-pi)
	* [wit-piDash](/projects/wit-piDash)
* [wit-mobile](/projects/wit-mobile)
* [wit-dash](/projects/wit-dash)
* Know Your Numbers
	* [wit-kyn](/projects/wit-kyn)
	* [kyn]