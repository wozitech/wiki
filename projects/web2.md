<!-- TITLE: WOZiTech web2 -->
<!-- SUBTITLE: Dockerised Enterprise LAN web server -->

# Overview
`web2` is  vagrant instanced launched on KVM, having 8GB and 2vCPU, running CentOS 7. The image is provisioned using Ansible, using the WOZiTech standard dockerised build.

Runs two containers:
1. WOZiTech-CMS (from Nexus)
```
docker login --username aylingw http://10.0.0.32:9080
docker pull 10.0.0.32:9080/wozitech-cms
```
2. wikijs (from docker hub) - `docker pull requarks/wiki:beta`

## Proxy
The web applications on web2 are exposed via the WOZiTech Proxy:
1. https://wozitech.asuscomm.com/wikijs - wikijs - port 8081
2.https://wozitech.asuscomm.com/cms - WOZiTech CMS - port 8080
# Upgrade
Having confirm installation configuration using trial build, reprovision `web2` using CentOS V8 (released Sept2019).

# Trail Build
`docker run --env-file=env_file -p 8080:1337 --name cms -d 10.0.0.32:9080/wozitech-cms`
`docker run -d -p 8081:3000 --name wiki --restart unless-stopped -e "DB_TYPE=sqlite" requarks/wiki:beta`

Updated the proxy with path contexts `cms` and `wiki` respectively.

## CMS
With CMS, can get to the public page, but the admin UI is anchored off `/` and thus fails. `strapi` allows customised path prefix on requests in `request.json`, but that only applies to the API not the admin UI.

## wiki
With wiki.js, it notices it's first time setup, but all the CSS, images and most significantly javascript (like that to render the setup screens) are all referenced from `/` too.

There is nothing in the wiki V2 documentation that suggests being able to configure a context root, so it looks like it's still not supported even in V2.