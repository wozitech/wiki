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
	
# Projects
* [wit-myBuses](/projects/wit-myBuses) - an Alexa skill to tell me when my next bus is for given locations
* [wit-domain](/projects/wit-domain)
* [wit-serve](/projects/wit-serve)
* [wit-pi](/projects/wit-pi)
	* [wit-piDash](/projects/wit-piDash)
* [wit-mobile](/projects/wit-mobile)
* [wit-dash](/projects/wit-dash)
* Know Your Numbers
	* [wit-kyn](/projects/wit-kyn)
	* [kyn]