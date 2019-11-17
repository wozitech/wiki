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

# Home Network
## Nexus
Available on https://nexus.wozitech.myddns.rocks - both the UI and the docker repository.

[Nexus Build](/projects/nexus)

## CMS
Available on https://cms.wozitech.myddns.rocks - the public, api and the admin.

Whilst working on SFC project at Sopra Steria, I did some evaluations on an API driven CMS; out of four contenders, [strapi](https://strapi.io/) came out the strongest for APIs and it supports MongoDB Atlas - which opens up the realms of streams an, stitch and the rest of the AWS set of services for integration and workflow. The admin UI was good, although pending a feature to add ACLs within the UI.

Having a [WOZiTech CMS](/projects/cms) with RESTful APIs is a good place to prototyping new applications.

## wiki2
Available on https://wiki.wozitech.myddns.rocks.

## Web2
`web1` was a dedicated node.js virtual server, running wiki.js version and a local monogdb instance. `web2` is a new virtual server instance, running multiple web applications as docker containers:
* WOZiTech CMS (strapi) - uses MongoDB Atlas
* wikijs V2 - uses built-in sqllite

[wozitech web2](/projects/web2)

## Proxy
Using DDNS service: https://www.dynu.com. It's free - bonus.

Registered wozitech.myddns.rocks. To update with current public IP, the following is ran every 30 minutes: `curl -X GET https://api.dynu.com/nic/update" -H "Authorization: d296a...lMDAK"`. where the `Authorization` header is BASE64 encoded `username:password`. This is done with a simple cron job `*/30 * * * * /bin/curl -X GET https://api.dynu.com/nic/update -H "Authorization: d29...AK" >/dev/null 2>&1`. **NOTE - not ansible automated owing to the sensitivity of keeping the Authorization code in github.**

Significantly, created CNAME records, referring back to the root DDNS domain, which hits this proxy, which uses multiple nginx _named (domain) servers_ for each of:
* wiki - `wiki.wozitech.myddns.rocks`
* cms - `cms.wozitech.myddns.rocks`
* nexus - `nexus.wozitech.myddns.rocks`

Separate "named servers" in nginx configuration for each of the above sub domains.

For security, using Lets Encrypt, and `gordonchan/auto-letsencrypt` excellent docker service which can create and manage SSL certificates, including aliases (e.g. www. prefix). A separate letsencrypt container for each domain.

The `letencrypts` container forces a reload on the `nginx` container having renewed renew certificates.