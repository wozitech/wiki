<!-- TITLE: Nexus -->
<!-- SUBTITLE: Nexus Build Notes -->

# Vagrant/KMS/Ansible
Have a local Nexus server on x.x.x.32.

Have a local nginx proxy server on x.x.x.10, with Lets Encrypt SSL, against DDNS `wozitech.asuscomm.com`.

# Using Nexus as Docker repo
Then to create and use nexus as a docker repo: https://blog.sonatype.com/using-nexus-3-as-your-repository-part-3-docker-images.

In short:
* Docker proxy repo - to buffer and cache DockerHub
* Docker hosted repo - private docker repo
* Docker group repo - to combine the proxy and hosted repos, so `search`/`pull` commands against against public and private images.

When creating Nexus docker repos, in addition to the Nexus base URL, each repo can have a dedicated listener. On my Nexus instance, port 9080 is for the hosted docker repo (private) and port 9081 is for the proxy docker repo. The group docker repo (includes hosted and proxy) has no dedicated port.

It is not expected that you can push to a group repo, and must instead opt to have the dedicated port (http connector) to push against. However,  this article shows a little nginx proxy rewrite to map GET requests to the group repository (called just docker in his case) and all PUT/POT/PATCH et al methods to the hosted repository, thus effectively looking like you're getting and pushing from the same repo: https://stackoverflow.com/questions/47178055/nexus3-push-to-docker-group-repo.

# nexus context root
The DDNS (via Asus Router) does not allow for subdomains, so only have one inbound name: wozitech.asuscomm.com - and that root is served direct to wiki.js - which does not support anything but `/` content root.

Using nginx reverse SSL proxy coming off proxy context path `/nexus`. To get this to work, have to set the Nexus context path to `/nexus` too.

Excited as I was to get this configuration working (I could login to the Nexus admin UI using https via the Extranet proxy with SSL, the `docker` client is not able to support docker repo with a context root other than `/`: https://support.sonatype.com/hc/en-us/articles/115013153887-Docker-Repository-Configuration-and-Client-Connection. This is forcing me to map ports through the reverse proxy to create the necessary SSL connection to the individual repo http connector ports.

So, if I am having to map additional ports (given I can't have DDNS subdomains) then I might aswell put Nexus context root back to `/` and serve Nexus from the same port 4431, via the nginx proxy and use nginx rewriting to make it look like `push` and `pull` are to the same repo.



## WOZiTech DEV LAN
From hosts on the DEV LAN, the firewall allows direct passthrough to the Nexus server (x.x.x.32)  on ENTERPRISE LAN. These LANs are isolated from Internet/wireless and therefore password security credentials without SSL OK.

So rather than going via the proxy, it makes sense use the repo specific HTTP connector ports which requires insecure docker connection.

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

# Upgrade
The nexus server is built using ansible; more specially the `ansible-thoteam.nexus3-oss` role. This playbook includes being able to [upgrade](https://github.com/ansible-ThoTeam/nexus3-oss#upgrade-nexus-to-latest-version) the Nexus installation to latest version.

## Take Snapshot
But before upgrading, using KVM snapshots to allow winding back if the upgrade is not successful (note, replace YYYYMMDD with today's date:
```
virsh shutdown enterprise_nexus
virsh snapshot-create-as --domain enterprise_nexus --name YYYYMMDD_snapshot --description "Nexus upgrade"
virsh start enterprise_nexus
virsh snapshot-list --domain enterprise_nexus
virsh snapshot-info --domain enterprise_nexus --snapshotname 20191119_snapshot
```

## Add a nexus ansible upgrade task only
To nexus.yml add (or uncomment):
```
    # upgrade nexus
    - role: ansible-thoteam.nexus3-oss
      become: yes
      nexus_upgrade: true
```

And then run the ansible provision through vagrant: `vagrant provision nexus`.

Note - the ansible task will evenetually fail owing to admin password having changed. It's not a problem on upgrade.

## Wait and login
Nexus takes a good age to start (it is java). But once the 8081 port has binded, login and confirm Nexus is running as expected.

## Delete the snapshot
All being well and the upgrade is successful:, delete the snapshot
```
virsh snapshot-list --domain enterprise_nexus
virsh snapshot-delete --domain enterprise_nexus --snapshotname YYYYMMDD_snapshot
```

### To revert the snapshot
If confirmation of Nexus upgrade has not been successful, then revert back to last snapshot, remembering to replace the YYYYMMDD:
```
virsh snapshot-list --domain enterprise_nexus
virsh shutdown --domain enterprise_nexus
virsh snapshot-revert --domain enterprise_nexus --snapshotname YYYYMMDD_snapshot --running 
```


