<!-- TITLE: Docker -->
<!-- SUBTITLE: A quick summary of Docker -->

# General Docker Commands

To attach to a running instance: `docker exec -it <container name | id> /bin/bash`.

To see a list of all docker images (not containers): `docker images`.

To update an existing image (not this does not impact containers running off that image: `docker pull <image ref>`.

To see a list of all running containers: `docker ps -a`.

To see a list of all local volumes: `docker volume ls`.

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