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
Have a local Nexus server, using nginx reverse SSL proxy coming off proxy context path `/nexus`. To get this to work, have to set the Nexus context path to `/nexus` too.

Then to create and use nexus as a docker repo: https://blog.sonatype.com/using-nexus-3-as-your-repository-part-3-docker-images.

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