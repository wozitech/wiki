---
title: WOZiTech web2
description: Dockerised Enterprise LAN web server
published: true
date: 2019-11-19T17:41:33.353Z
tags: 
---

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
1. https://wiki.wozitech.myddns.rocks - wikijs -  via `web2` host port 8081
2. https://cms.wozitech.myddns.rocks - CMS - via `web2` host port 8080


# Trail Build
`docker run --env-file=env_file -p 8080:1337 --name cms -d 10.0.0.32:9080/wozitech-cms`
`docker run -d -p 8081:3000 --name wiki --restart unless-stopped -e "DB_TYPE=sqlite" requarks/wiki:beta`

Updated the proxy with path contexts `cms` and `wiki` respectively.

# Ansible/Docker
The CMS and wiki containers are deployed using ansible, with images taken from the WOZiTech Nexus (local) docker repo.

That docker repo requires authentication. `$ANSIBLE_ARGS` environment variable is consumed via vagrant and passed to ansible. On my DevOps vagrant/terraform console desktop, I've set `$ANSIBLE_ARGS` from within `~/.bash_profile` to:
```
--extra-vars "NEXUS_USERNAME=docker_read NEXUS_PASSWORD=<guess!!!!>"
```

## CMS

## wiki
ansible with `docker_container` creates a postrges container and a wikijs container. postgres container includes username/password in the ansible script, because the container is not port mapped to host (only available to other docker containers on that host. The wikijs container is linked back to the postgres container.

wiki container has a volume mapped to `/var/wiki/data`. Within the wikijs container, runs as `node` user with UID/GID of 1000.
postgres container has a volume mapped `/var/wiki/postgresdata`. Within container, runs as `root`.

But to get the wikijs and postgres running together had to make both volumes with permissions "777"; was gettign atrange postrges error about permission denied on _global/pg_filenode.map_.

The V1.x wiki was imported using the original github details; which has set those details as the default storage engine for wiki2.

Have enabled the postgres advanced search (full text); now searches on content within the page unlike the basic search.

### Update
Logon to the web2 host (`vagrant ssh web1`):
```
docker stop wikijs
docker rm wikijs

docker image pull 10.0.0.32:9081/requarks/wiki
```

Then reprovision via ansible: `vagrant provision web2`.



### Authentication Google
Has created a Google App called "WOZiTech wiki", but unverified domain (on `wozitech.myddns.rocks`) is preventing me from adding the necessary wikijs Google redirects.