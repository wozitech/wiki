---
title: Docker
description: A quick summary of Docker
published: true
date: 2020-03-15T16:50:51.411Z
tags: 
---

# General Docker Commands

To attach to a running instance: `docker exec -it <container name | id> /bin/bash`.

To see a list of all docker images (not containers): `docker images`.

To update an existing image (not this does not impact containers running off that image: `docker pull <image ref>`.

To see a list of all running containers: `docker ps -a`.

To see a list of all local volumes: `docker volume ls`.

To monitor the logs: `docker logs --follow <container name | id>`.

To start/stop container: `docker stop <container name | id>` or `docker start <container name | id>`.

To delete a container (having stopped it): `docker rm <container name | id>`.

To launch a new instance (example only):
```
docker run -p 10080:80 \
        -e "PGADMIN_DEFAULT_EMAIL=warren.ayling@wozitech-ltd.co.uk" \
        -e "PGADMIN_DEFAULT_PASSWORD=thisIsNotMySecret" \
        -d dpage/pgadmin4 \
				-v "/home/pgadmin/var:/var/lib/pgadmin"
```

_10080 is the local port mapping on to 80 within the container._
_/home/pgadmin/var is the local directory mapping to the /var/lib/pgadmin directory within the container.

## Build
`docker build -t <tag> .`

With repeated local build it is often necessary to run `docker image prune` regular to clean up dangling images. It is also usefuyl to frequently prune dead containers: `docker container prune`.

### Multi-Stage Build
In any cloud-first development environment, especially when applications are being built from source code (ergo, Golang, Java and rust), you may have separate Dockerfiles to create the "builder" image and then the "runtime" image.

A multi-stage Dockerfile can be used instead; you can pass in a `target` to build up to a given stage (namely, the "builder" image).

https://docs.docker.com/develop/develop-images/multistage-build/

## Volumes
Volumes are the preferred method to present local storage to containers. Volumes are managed by docker and therefore you don't get the usual shanigans associated with unix file permissions for creating local directories.

When a docker container is started (`docker run`) specifying a volume, if it does not exist, the docker daemon will create it.

Simply replace each "-v" with "--mount". Using the above example:
```
docker run -p 10080:80 \
        -e "PGADMIN_DEFAULT_EMAIL=warren.ayling@wozitech-ltd.co.uk" \
        -e "PGADMIN_DEFAULT_PASSWORD=thisIsNotMySecret" \
        -d dpage/pgadmin4 \
				--mount source=pgadmin,target=/var/lib/pgadmin
```

# Networks
No need to link containers, simply manage you're own networks.

Creating private networks is simple: `docker network create myprivnetwork`.

To create a container presented on that private network:
```
docker run -d --name mydb --network=myprivnetwork -p 9042:9042 cassandra:3
```

To create a client container:
```
docker run --network=mynetwork -e "DATABASE=cassandra" -e "DB_HOST=mydb" -d my:container
```